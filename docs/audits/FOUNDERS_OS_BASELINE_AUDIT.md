# FounderOS Baseline Audit

Status: source-backed baseline audit  
Audit date: 2026-09-14, Asia/Bangkok  
Repository: `Booomslam/FounderOS-EVNav`  
Revision: `d5e565ea1ee82069e17cd176a33c2f272619e002`  
Scope: existing repository only  
Audit mode: read-only for product code; only this report and Archify audit artifacts were added  

## 1. Executive summary

FounderOS is a credible application shell and integration prototype, but it is not yet a reliable multi-agent company operating system.

The repository contains a polished Next.js interface, a substantial SQLite repository layer, validated schemas, persisted chat history, several genuine read connectors, a GBrain adapter, and working route-level agent chat. Those parts are more than mock screens. The test suite, typecheck, and production build pass under Node 22.

The central product claim is nevertheless ahead of the runtime. The current "Conductor" chooses one agent for a request, or broadcasts independently to all registered agents. It does not create a plan, delegate durable child tasks, enforce departmental boundaries, wait for dependencies, consolidate evidence, request approval, resume interrupted work, or provide exactly-once external actions. The organization chart, workflows, skills, and task board are primarily persisted presentation data rather than execution control structures.

The largest risks are not visual. They are control-plane risks:

1. External write routes can send email, Slack messages, social DMs, or mutate mailbox state without a durable approval record, action policy, or idempotency key.
2. Authentication is an optional shared token. Authorization, tenant isolation, department isolation, and per-agent permission enforcement are absent.
3. Agent runs have final-result persistence but no durable state machine, claim/lease model, retry contract, checkpoint, cancellation, or recovery path.
4. GBrain is meaningfully integrated at the adapter level, but the executable was unavailable during this audit and no local brain documents were present. The larger documented Optimal Engine pipeline is not implemented.
5. Production dependencies currently report 18 security advisories, including 2 critical and 9 high, and the documented Node 18+ range is too broad: dependency installation failed on Node 26 but succeeded on Node 22.

Recommendation: keep the existing product shell and repository conventions, then add a small explicit TypeScript control plane. Use the current Vercel AI SDK for the first orchestration MVP. Do not introduce a second orchestration framework until durable tasks, server-side policies, approvals, audit events, and action idempotency have clear contracts. Evaluate Paperclip later as a control-plane replacement or integration, OpenClaw later as a channel/runtime gateway, and the OpenAI Agents SDK later for richer agent handoffs and resumable execution. LangGraph is not needed for the MVP.

## 2. Archify Architecture Findings

Archify findings and delivery evidence appear in section 3. The maturity rules below define how all Archify and manual evidence was interpreted.

The audit used five evidence levels:

| Label | Meaning |
|---|---|
| `REAL` | Implemented in source and directly verifiable in this repository or local test environment. |
| `PARTIAL` | Meaningful implementation exists, but the end-to-end capability, controls, or live configuration is incomplete. |
| `DEMO / SEEDED` | UI, static code, or persisted sample data demonstrates intent but is not a dependable operational capability. |
| `STUB` | Explicit placeholder behavior. |
| `NOT PRESENT` | No implementation was found after source, route, schema, test, and configuration searches. |

Important constraints:

- A UI label is not runtime evidence.
- An environment variable check is not proof of a working integration.
- A persisted row is not proof of a durable job engine.
- A system prompt is not a security boundary.
- Passing local tests is not proof of a live external integration or production deployment.
- No unavailable external system was treated as working.

### Archify artifact and visual validation

Archify was run before the manual audit, as requested. The installed Archify CLI can validate repository source references and produce an explorable architecture artifact, but it does not contain an automatic repository-discovery command. Therefore the diagram specification was constructed from verified source paths, then validated against the repository.

Artifacts:

- `docs/audits/archify/founder-os-runtime-boundaries.html`
- `docs/audits/archify/founder-os-runtime-boundaries.architecture.json`
- `docs/audits/archify/founder-os-runtime-boundaries.visual-check.html`
- `docs/audits/archify/founder-os-runtime-boundaries.visual-check.json`
- Four light and dark viewport captures under `docs/audits/archify/`

Delivery evidence:

| Check | Result |
|---|---|
| Repository source references | 25 validated |
| Archify showcase checks | 9 of 9 passed |
| Errors | 0 |
| Warnings | 0 |
| Visual review | Passed after one focused correction |
| Viewports | 1440x900 and 2048x1320, light and dark |
| Spec SHA-256 | `b33c55f86a42df105cc071f3142439f0e863a2d2f13f3a0454b24f58dc2dd298` |
| HTML SHA-256 | `8e3c208b38268a0f468039c5d76002d31e2714424d689b44dac460eb08f2b64d` |

The diagram distinguishes the presentation layer, route handlers, agent/runtime modules, connector adapters, persistence, and external boundaries. It deliberately marks the missing durable orchestration and policy plane rather than presenting seeded workflows as executable.

## 3. Verified Repository Architecture

### 3.1 Runtime layers

```text
Browser UI
  -> Next.js App Router pages and client components
  -> Next.js route handlers
  -> Agent chat, conductor, runtime, and connector modules
  -> Repository layer and Zod schemas
  -> SQLite database, local files, and external APIs
```

Primary source anchors:

- Agent registry and invocation: `lib/agents/runtime.ts:8-85`
- Static agent definitions: `lib/agents/real.ts:114-540`
- Agent chat: `lib/agents/chat.ts:18-71`
- Conductor routing: `lib/agents/conductor.ts:26-64`
- LLM gateway: `lib/connectors/llm.ts:40-125`
- Repository implementations: `lib/db.ts`
- Domain validation: `lib/schemas.ts`
- Connector aggregation: `lib/connectors/index.ts:37-85`
- GBrain adapter: `lib/connectors/gbrain.ts`
- Optional shared-token gate: `middleware.ts:4-45`

### 3.2 Technology stack

| Concern | Verified implementation |
|---|---|
| Web application | Next.js 14 App Router, React 18, TypeScript |
| Styling | Tailwind with repository theme variables |
| Persistence | `better-sqlite3` through `lib/db.ts` repositories |
| Validation | Zod schemas in `lib/schemas.ts` |
| Model invocation | Vercel AI SDK through an AI gateway adapter |
| Tests | Vitest, Node environment |
| Knowledge | GBrain CLI adapter with local substring-search fallback |
| Deployment definition | Not found |
| CI definition | Not found |

### 3.3 High-centrality modules

A static import-count pass identified these central boundaries:

| Module | Approximate importing files | Audit significance |
|---|---:|---|
| `lib/schemas` | 70 | Domain contract and validation hub |
| `lib/data` | 42 | Shared data access singleton |
| `lib/connectors/types` | 26 | Connector status contract |
| `lib/creds` | 15 | Credential resolution and mutation |
| `lib/connectors/social` | 10 | Social integration boundary |
| `lib/connectors/gbrain` | 10 | Knowledge boundary |
| `lib/connectors/email` | 10 | Read and write email boundary |
| `lib/db` | 9 | Persistence boundary |

Changes to these modules have broad blast radius and should receive contract tests before control-plane work begins.

## 4. Archify vs Source-Code Discrepancies

Archify correctly identified the route, agent, connector, repository, security, and external-system boundaries represented in the durable diagram. Its source-reference validation proved that cited files existed at the audited revision. Archify did not establish runtime maturity, reachability, or production readiness.

The main discrepancy was semantic: structural detection can make task, workflow, agent, skill, and organization modules appear to form an orchestration platform. Manual tracing showed that those relationships stop at UI rendering, persisted catalog data, single-agent routing, or independent broadcast. The durable diagram was therefore authored to show the verified runtime and label the missing control plane. Source behavior takes precedence over the diagram.

No automatic Archify repository-discovery agent was available in the installed CLI. The architecture specification was constructed from manual source paths, checked with Archify repository reference validation, then independently traced in source. This limitation is explicit so the artifact is not mistaken for an automated code graph.

## 5. What Is Real vs Demo

| Claim or implication | Source-backed finding | Classification |
|---|---|---|
| Conductor orchestrates a company | It selects one agent or broadcasts independent requests. It does not plan, delegate durable work, or consolidate. | `PARTIAL` |
| Agents work autonomously on task cards | The task board polls and manually patches status. No agent runtime path updates task status. | `DEMO / SEEDED` |
| Workflows represent operating execution | Workflows and steps are stored and rendered, while add-step behavior is client-local. No executor was found. | `DEMO / SEEDED` |
| `parentId` represents delegation | It builds a hierarchy for display. Runtime routing does not use it. | `DEMO / SEEDED` |
| Agent tool labels define permissions | Displayed tools and hardcoded chat tools are separate. No policy engine enforces the displayed model. | `DEMO / SEEDED` |
| Optimal Engine governs knowledge | The pipeline appears in documentation and copy, but no Source -> Signal -> Claim -> Fact -> Memory engine was found. | `NOT PRESENT` |
| Node 18+ is supported | Node 22 installs and builds. Node 26 fails compiling `better-sqlite3`. The broad compatibility claim is not verified. | `PARTIAL` |
| Production start respects platform port | `npm start` fixes port 4100 while the README references platform `PORT`. | `PARTIAL` |

### Capability maturity matrix

| Capability | Classification | Evidence and limitation |
|---|---|---|
| Application UI shell | `REAL` | Multiple functional Next.js pages and components build successfully: `app/`, `components/`, `package.json`. |
| Agent registry | `REAL` | In-memory runtime registry and static definitions exist in `lib/agents/runtime.ts` and `lib/agents/real.ts`. |
| Agent chat | `REAL` | Messages persist and the configured path supports tools: `lib/agents/chat.ts:18-71`, `lib/connectors/llm.ts:70-114`. Live LLM was not configured. |
| Agent run history | `REAL` | `agent_runs` and chat messages persist: `lib/db.ts`, `lib/agents/runtime.ts:28-56`, `lib/agents/chat.ts:33-71`. History is scoped only by agent ID. |
| Agent runtime | `PARTIAL` | Synchronous invocation exists without a durable worker lifecycle: `lib/agents/runtime.ts:28-56`. |
| Multi-agent orchestration | `PARTIAL` | Selection and independent broadcast exist without plan/dependency/consolidation: `lib/agents/conductor.ts:26-64`, `lib/agents/runtime.ts:58-85`. |
| Agent delegation | `NOT PRESENT` | No durable child-task path was found; `parentId` is consumed by `lib/hierarchy.ts:21-31`, not the runtime. |
| Chief of Staff behavior | `PARTIAL` | Conductor selects one agent but cannot manage a multi-step outcome: `lib/agents/conductor.ts:26-64`. |
| Agent permissions | `PARTIAL` | Chat tools are hardcoded and a prompt asks for read-only behavior: `lib/agents/real.ts`, `lib/agents/chat.ts:18-30`. No policy engine exists. |
| Department isolation | `NOT PRESENT` | Departments affect grouping, not authorization: `lib/hierarchy.ts:38-54`, `app/org/page.tsx:85-305`. |
| Task CRUD | `REAL` | Repository-backed CRUD exists: `app/api/agents/work/route.ts:9-71`, `lib/db.ts:679-739`. |
| Task execution | `DEMO / SEEDED` | Agent work panel explicitly describes the runner as scheduled-on-paper: `components/AgentWorkPanel.tsx:3-8,198-200`. |
| Task dependencies | `NOT PRESENT` | No dependency fields or scheduler exist in `lib/schemas.ts:251-267` or `lib/db.ts:162-177`. |
| Workflow definitions | `DEMO / SEEDED` | Seeded workflow rows and visual steps exist: `lib/seed.ts:1420-1585`, `components/WorkflowMap.tsx:3-10`. |
| Workflow execution | `NOT PRESENT` | The schema/page/render path ends at `lib/schemas.ts:428-459`, `app/workflows/page.tsx:10-24`, and `components/WorkflowMap.tsx`; no executor was found. |
| Checkpoint and resume | `NOT PRESENT` | Chat and run code stores final records only: `lib/agents/chat.ts:33-71`, `lib/agents/runtime.ts:28-56`. |
| Retry and backoff policy | `NOT PRESENT` | No job-level attempt/retry contract appears in task schema or runtime: `lib/schemas.ts:251-267`, `lib/agents/runtime.ts`. |
| Action idempotency | `NOT PRESENT` | No general action key exists; provider-ID upsert in `app/api/webhooks/manychat/route.ts:7-30` is a narrow exception. |
| Approval engine | `NOT PRESENT` | No approval entity, route, or enforcement point appears in `lib/schemas.ts`, `lib/db.ts`, or `app/api/`. |
| External side effects | `PARTIAL` | Real writes exist without central governance: `app/api/comms/reply/route.ts:14-46`, `app/api/social/dm/reply/route.ts:9-51`. |
| Email integration | `PARTIAL` | Read and SMTP send code is real in `lib/connectors/email.ts:55-82`; local status was not configured. |
| Calendar integration | `PARTIAL` | A read adapter exists in `lib/connectors/gcal.ts`; local status was not configured. |
| GitHub integration | `NOT PRESENT` | No GitHub product adapter appears in `lib/connectors/` or relevant `app/api/` routes. |
| Document integration | `PARTIAL` | `lib/connectors/notion.ts` and `lib/connectors/obsidian.ts` exist without a governed document lifecycle. |
| CRM integration | `PARTIAL` | `lib/connectors/attio.ts` and `lib/connectors/ghl.ts` exist; local configuration was absent. |
| Finance integration | `PARTIAL` | Payment reads exist in `lib/connectors/payments.ts`; no governed transaction capability was found. |
| Social integration | `PARTIAL` | Real and queued paths exist in `lib/social.ts`, `lib/connectors/manychat.ts`, and `app/api/social/`; controls are incomplete. |
| Connector health dashboard | `REAL` | Centralized explicit status aggregation exists: `lib/connectors/index.ts:37-85`, `app/api/connections/route.ts`. |
| Company knowledge adapter | `PARTIAL` | Doctor/query/capture and fallback exist: `lib/connectors/gbrain.ts:68-297`, `lib/brain.ts:1-45`; GBrain was unavailable locally. |
| Vector search | `PARTIAL` | GBrain supplies a query boundary in `lib/connectors/gbrain.ts`; graph vectors in `lib/schemas.ts:193-226` are lexical fingerprints. |
| Optimal Engine | `NOT PRESENT` | The pipeline is a README claim at `README.md:109-127`; no matching schema, repository, or runtime was found. |
| Decision memory | `NOT PRESENT` | No decision entity appears in `lib/schemas.ts` or `lib/db.ts`. |
| Fact governance | `NOT PRESENT` | No claim/fact confidence, conflict, supersession, or review entity appears in `lib/schemas.ts` or `lib/db.ts`. |
| Knowledge access control | `NOT PRESENT` | GBrain and brain dump paths lack department/private authorization: `lib/connectors/gbrain.ts`, `app/api/brain/dump/route.ts:8-33`. |
| Audit trail | `PARTIAL` | Messages, runs, and broadcasts persist through `lib/agents/chat.ts` and `lib/agents/runtime.ts`; no append-only action ledger exists. |
| Cost and token accounting | `NOT PRESENT` | Model results are handled in `lib/connectors/llm.ts:70-114`, but no usage/cost entity appears in `lib/schemas.ts` or `lib/db.ts`. |
| Authentication | `PARTIAL` | Optional shared token only: `middleware.ts:4-45`, `lib/access-gate.ts:19-29`. |
| Authorization | `NOT PRESENT` | No user, role, scope, or policy check appears beyond the shared gate in `middleware.ts` and `lib/access-gate.ts`. |
| Secret handling | `PARTIAL` | Gitignore, allowlists, masking, and mode 0600 exist: `.gitignore:17-20`, `lib/creds.ts:41-139`, `app/api/keys/route.ts`. |
| Automated tests | `REAL` | 963 tests passed under Node 22 using `vitest.config.ts` and `tests/`; live system classes remain absent. |
| CI/CD | `NOT PRESENT` | No `.github/workflows`, Dockerfile, or platform deployment manifest was found. |
| Production readiness | `PARTIAL` | `package.json` build succeeds, but `middleware.ts`, runtime schemas, dependency audit, and absent deployment evidence block production trust. |

## 6. Agent Runtime Trace

### 6.1 Direct agent chat: Data Agent example

```text
AgentChat component
  -> POST /api/agents/{agentId}/chat
  -> route validates message and context
  -> chatWithAgent(agentId, message)
  -> persist user message
  -> load all history for agentId
  -> resolve hardcoded RuntimeAgent.chatTools
  -> generateText through AI gateway, up to 6 steps
  -> persist tool-call metadata and assistant message
  -> return assistant response
```

Evidence:

- Client request: `components/AgentChat.tsx:27-47`
- Route: `app/api/agents/[id]/chat/route.ts:10-39`
- Prompt, history, tools, persistence: `lib/agents/chat.ts:18-71`
- Model and tool loop: `lib/connectors/llm.ts:70-114`

The concrete agent traced for this audit is `data-agent`. Its definition is in `lib/agents/real.ts:306-358`. Its non-chat `run()` path calls GBrain overview/search operations. Its chat path exposes the `searchGBrain` read-only tool. The generic prompt is constructed at runtime from this agent's registered name and description plus the shared read-only instructions in `lib/agents/chat.ts:18-30`.

When an LLM is configured, the model is actually invoked through `generateWithGateway()` and Vercel AI SDK `generateText()` in `lib/connectors/llm.ts:70-114`. The provider path is the Vercel AI Gateway. The model is read from `AI_GATEWAY_MODEL` or `LLM_MODEL`, otherwise it defaults to `anthropic/claude-sonnet-5` at `lib/connectors/llm.ts:40-45`. During this audit the LLM connector reported `not_configured`, so live provider execution was not claimed.

The result returns synchronously in the HTTP request. Tool calls and the assistant response are persisted before the UI receives JSON and appends the assistant turn. No asynchronous worker, durable continuation token, or resumable state participates in this path.

Reliability findings:

- User input is persisted before model execution. A model failure can leave an unmatched user message.
- There is no transaction across message, run, tool calls, and final response.
- History is grouped by agent ID, not user, company, thread, or task.
- Earlier tool turns are removed before subsequent model calls in `lib/connectors/llm.ts:89-91`.
- Tool availability is a useful code-level allowlist, but there is no contextual policy check per user, task, department, or data record.

### 6.2 Conductor chat

```text
ConductorChat component
  -> POST /api/agents/conductor/chat
  -> routeConductorMessage
  -> optional explicit @agent match, otherwise one LLM routing choice
  -> chatWithAgent for exactly one selected agent
  -> return selected agent response
```

Evidence: `components/ConductorChat.tsx:21-41`, `app/api/agents/[id]/chat/route.ts:32-36`, and `lib/agents/conductor.ts:26-64`.

This is a router, not a manager-agent loop. It does not create subtasks, preserve a plan, wait for results, validate evidence, or synthesize multiple agent outputs.

### 6.3 Organization broadcast

```text
ConductorCard on /org
  -> POST /api/agents/broadcast
  -> insert broadcast row
  -> Promise.all over every registered agent
  -> call respond() or run() independently
  -> insert one reply row per agent
  -> return replies
```

Evidence: `components/ConductorCard.tsx:27-48`, `app/api/agents/broadcast/route.ts:12-25`, and `lib/agents/runtime.ts:58-85`.

Broadcast provides parallel fan-out, but it is not coordinated delegation. There is no assignment contract, shared task, dependency graph, final consolidation, or failure policy.

## 7. Task Runtime Trace

### 7.1 Agent work panel

The agent work panel performs repository-backed CRUD through `/api/agents/work`. Its own source states that tasks and cron definitions persist while the runner is scheduled-on-paper and ships later: `components/AgentWorkPanel.tsx:3-8` and `components/AgentWorkPanel.tsx:198-200`.

Task schema fields are limited to ID, agent ID, title, status, and timestamps: `lib/schemas.ts:251-267`. The table and repository operations are in `lib/db.ts:162-177` and `lib/db.ts:679-739`.

Missing execution fields include:

- Parent task and dependency IDs
- Input and output artifact references
- Attempt, retry, lease, and heartbeat state
- Required policy and approval IDs
- Idempotency key
- Checkpoint and resumable state
- Failure class and recovery decision
- Actor, requester, and company scope

### 7.2 Main task board

`components/TaskBoard.tsx:32-63` polls and patches task status. The source search found no agent runtime path that advances these cards. The UI statement that agents advance cards is therefore a demonstration claim, not a verified runtime behavior.

### 7.3 Workflows

Workflow tables and schemas exist at `lib/db.ts:301-318` and `lib/schemas.ts:428-459`. The page reads and renders them at `app/workflows/page.tsx:10-24`. `components/WorkflowMap.tsx:3-10` explicitly calls the content seeded and real-ready, while step addition is local component state at `components/WorkflowMap.tsx:147-172`.

No executor, dependency scheduler, approval pause, job claim, retry loop, or workflow-run record was found.

## 8. Organization Model Analysis

The organization model is a real data and visualization feature:

- Agents contain department, tier, tools, parent ID, and instance fields: `lib/schemas.ts:17-32`.
- Parent IDs build recursive display trees: `lib/hierarchy.ts:21-31`.
- Departments group agents: `lib/hierarchy.ts:38-54`.
- `/org` loads the repository and renders leadership and department branches: `app/org/page.tsx:85-305`.

It is not an execution authority model:

- `parentId` is not consulted by the conductor or runtime.
- Department and tier do not restrict tools, records, retrieval, or delegation.
- Displayed `tools` metadata does not automatically populate `RuntimeAgent.chatTools`.
- The venture lens dims the same shared roster and database rather than selecting an isolated company scope: `app/org/page.tsx:89-93`.

Conclusion: organization structure is `REAL` as UI/data and `DEMO / SEEDED` as runtime authority.

## 9. Memory / Knowledge Analysis

### 9.1 GBrain adapter

`lib/brain.ts:1-45` defaults to the GBrain connector, with a local fallback and a test stub. `lib/connectors/gbrain.ts` implements doctor, query, stats, capture, and local substring search. `lib/brain-dump.ts:34-99` writes timestamped markdown locally and can immediately invoke capture/embed.

This is a meaningful adapter, but current local evidence was:

- GBrain connector status: `error`
- `gbrain` executable: not found on PATH
- Local brain-store markdown documents: 0

Therefore live semantic retrieval, capture, embedding, and provenance could not be verified.

SQLite records and local markdown files survive an application restart. An external GBrain store should also survive the web process, but its live persistence could not be verified because the executable was unavailable. No automatic path from ordinary agent chat to trusted knowledge was found. The brain-dump API can write and optionally capture arbitrary submitted content without human fact approval, so captured content must not be assumed trusted.

### 9.2 Memory integrity

Current memory records provide paths, snippets, and source strings, but there is no implemented policy for:

- Source trust levels
- Fact versus claim distinction
- Conflicting assertions
- Supersession and expiry
- Human validation
- Department/private visibility
- Deletion and retention
- Decision outcomes and later review

`BrainGraphNode` includes a 64-dimensional lexical fingerprint and projected coordinates in `lib/schemas.ts:193-226`. This supports graph display and should not be described as proof of production vector search.

### 9.3 Optimal Engine

The README describes a Source -> Signal -> Claim -> Fact -> Memory flow at `README.md:109-127`. No corresponding engine, entities, repository methods, policy transitions, or tests were found. Classification: `NOT PRESENT`.

## 10. Security & Permission Analysis

### 10.1 Authentication

`middleware.ts:4-45` provides an optional application-wide shared token. If the token is unset, requests are allowed. `lib/access-gate.ts:19-29` compares a query parameter, cookie, or authorization token to the configured value.

Strengths:

- The cookie is HTTP-only and SameSite Lax.
- The cookie becomes Secure under HTTPS.
- The middleware matcher covers application routes and APIs.

Limits:

- There are no user identities or sessions.
- There is one shared credential and one effective role.
- Query-string token intake can expose a credential through history, logs, referrers, or screenshots.
- No rate limit or brute-force control was found.

### 10.2 Authorization and isolation

No RBAC, ABAC, per-record ownership, workspace boundary, department boundary, or tool-action policy engine was found. Any caller who passes the optional gate receives the same API authority. The visual organization model is not authorization.

### 10.3 Secrets

Positive controls:

- `.env.local` and credential files are gitignored: `.gitignore:17-20`.
- Writes are restricted to known key slots in relevant routes.
- `.env.local` is written with mode 0600: `lib/creds.ts:73-96`.
- Connection APIs do not echo submitted secret values.

Production gaps:

- Credentials are process-wide rather than company, user, connector, and action scoped.
- There is no secret manager, automatic rotation, expiration, or use audit.
- Canonical local credential files are trusted implicitly by `lib/creds.ts:41-48`.
- There is no proof that secrets are excluded from every future prompt or tool result.

### 10.4 Prompt and data trust

`lib/agents/chat.ts:23-29` instructs agents to be read-only and not invent data. This is useful behavior guidance, not enforcement. External email, CRM, document, and social content can become model input. No systematic untrusted-content tagging, instruction stripping, output DLP, or policy-mediated tool executor was found.

Current chat tool sets appear read-only, so the traced Data Agent cannot directly call the email, Slack, mailbox, credential, or DM mutation routes through its declared tools. That is a useful present limitation, but not a durable boundary: the mutation APIs remain available to any caller that passes the optional shared gate, and a future write tool could bypass policy unless all mutations are centralized.

## 11. Approval / Side-Effect Analysis

Real mutation paths include:

| Route or module | Side effect | Current control gap |
|---|---|---|
| `app/api/comms/reply/route.ts:14-46` | Send Slack or email reply | No approval, idempotency, or durable action audit |
| `lib/connectors/email.ts:55-82` | SMTP email send | No action binding or duplicate prevention |
| `app/api/comms/email/action/route.ts:7-20` | Archive, trash, mark, star email | No per-action authorization or approval |
| `app/api/social/dm/reply/route.ts:9-51` | Send ManyChat DM | No approval or central policy evaluation |
| `app/api/brain/dump/route.ts:8-33` | Write and optionally capture knowledge | No trust level, review, or provenance approval |
| `app/api/connections/connect/route.ts:28-58` | Write connector credentials | Shared-token authority only |
| `app/api/keys/route.ts:20-31` | Write model keys | Shared-token authority only |

`app/api/social/posts/route.ts:21-40` queues a post but does not publish it, which is safer than direct publication. The ManyChat webhook has a narrow duplicate defense through upsert by provider ID, but the webhook secret is optional: `app/api/webhooks/manychat/route.ts:7-30`.

Approval capability classification: `NOT PRESENT`. An approval UI alone would not close the gap. Enforcement must sit immediately before the side-effect adapter and bind the approved action payload hash, actor, scope, expiry, and idempotency key.

| Requested level | What exists today | Maturity | Required modification |
|---|---|---|---|
| LEVEL 0, Read-only | Multiple read connectors and chat tools exist | `PARTIAL` | Enforce user, company, department, data, and tool scopes server-side |
| LEVEL 1, Internal draft | Queued posts and generated text can act as drafts | `PARTIAL` | Add an explicit draft state that cannot cross an external boundary |
| LEVEL 2, External communication | Email, Slack, and social DM sends exist | `PARTIAL` | Route all sends through exact-content approval and idempotent execution |
| LEVEL 3, External system modification | Mailbox mutations and credential writes exist | `PARTIAL` | Bind approval to exact provider, resource, operation, and parameters |
| LEVEL 4, Financial/legal commitment | Payment connector is read-oriented; no verified spend/legal execution path found | `NOT PRESENT` | Keep absent for MVP; later require named approver, limits, stronger authentication, and complete evidence |

No approved-action binding exists today, so there is no protection against an approval being reused for different parameters. No general retry/resume layer exists, so direct retries can duplicate side effects.

## 12. Integration Status

### 12.1 Current local status

The connector aggregation was invoked without printing secret values.

| Connector | Local status during audit |
|---|---|
| Local stack | `connected` |
| GBrain | `error` |
| LLM | `not_configured` |
| WhatsApp | `not_configured` |
| Zernio | `not_configured` |
| Beehiiv | `not_configured` |
| ManyChat | `not_configured` |
| Attio | `not_configured` |
| WebinarJam | `not_configured` |
| Trakyo | `not_configured` |
| Meta Ads | `not_configured` |
| GoHighLevel | `not_configured` |
| Arcads | `not_configured` |
| Wispr | `not_configured` |
| Obsidian | `not_configured` |
| Miro | `not_configured` |
| Email | `not_configured` |
| Calendar | `not_configured` |
| Slack | `not_configured` |
| Payments | `not_configured` |
| Notion | `not_configured` |

This status does not say the code is fake. It says live end-to-end behavior was not available for verification on this machine.

### 12.2 Adapter quality

Several adapters execute genuine provider requests, including email, Slack, Stripe, Attio, Notion, Google Calendar, ManyChat, and GBrain. Others are status checks or credential-presence checks. In particular, a success result from `envIntegrationRun` in `lib/agents/real.ts:77-84` means a credential exists, not that a provider operation succeeded. Trakyo and Meta Ads are currently status-oriented.

Each integration should ultimately distinguish:

1. Code adapter exists.
2. Credentials are configured.
3. Provider health check succeeds.
4. Read contract test succeeds against a sandbox.
5. Write contract test succeeds with approval and idempotency.
6. Production scope and ownership are approved.

## 13. Test / Build Evidence

### 13.1 Commands and results

The repository initially had no installed dependencies. A normal `npm ci` under Node 26.8.2 failed while compiling `better-sqlite3` 11.10.0 because of incompatible V8 APIs. Dependencies were then installed with an ephemeral Node 22 runtime, without changing project dependency files.

| Check | Result |
|---|---|
| `npm ci` under Node 22 | Passed |
| Vitest under Node 22 | 111 files, 963 tests passed |
| TypeScript `tsc --noEmit` under Node 22 | Passed |
| Next.js production build under Node 22 | Passed, 24 static pages generated |
| Dedicated lint script | Not present |
| Build lint/type phase | Passed |

Test environment is configured in `vitest.config.ts` with a Node environment and isolated credential variables. Coverage is strongest at unit, component rendering, repository, connector adapter, and route-handler levels.

Important missing test classes:

- Browser end-to-end tests
- Multi-user and authorization tests
- Department isolation tests
- Approval binding and expiry tests
- Task claim, lease, retry, crash, and resume tests
- Duplicate external action tests
- Live provider sandbox contract tests
- Prompt-injection and data-exfiltration tests
- Backup/restore and migration tests
- Load and concurrency tests

### 13.2 Dependency risk

`npm audit --json` reported 18 vulnerabilities: 3 low, 4 moderate, 9 high, and 2 critical. Direct dependencies named in the report include critical advisories for Next.js and Vitest, high advisories for Nodemailer, ImapFlow, and PostCSS, and a low advisory affecting the AI SDK dependency chain.

This count is a triage input, not proof that every advisory is exploitable in this application. Production readiness requires upgrading, rebuilding, rerunning tests, and reviewing reachable vulnerable paths. Audit suggestions that require major-version changes should be handled deliberately rather than by an automatic force fix.

### 13.3 Deployment

No Dockerfile, platform manifest, Vercel configuration, Railway configuration, or GitHub Actions workflow was found. The README references Railway behavior, but `npm start` fixes port 4100. A production deployment and rollback procedure is therefore not source-verifiable.

## 14. Gap Matrix

| Capability | FounderOS today | Maturity | Target requirement | Gap | Recommended action |
|---|---|---|---|---|---|
| CEO chat interface | Conductor chat routes one request to one selected agent: `components/ConductorChat.tsx`, `lib/agents/conductor.ts` | `PARTIAL` | CEO conversation with durable plan, decisions, approvals, and evidence | Router has no managed outcome | `EXTEND` |
| Direct department chat | Agent chat UI, route, LLM tools, and persisted messages exist: `components/AgentChat.tsx`, `lib/agents/chat.ts` | `REAL` | Company/user/conversation scoped direct chat | History is keyed only by agent ID | `REFACTOR` |
| Chief of Staff orchestration | Chooses one agent or uses a separate all-agent broadcast: `lib/agents/conductor.ts`, `lib/agents/runtime.ts:58-85` | `PARTIAL` | Plan, delegate, wait, validate, consolidate, escalate | No durable management loop | `EXTEND` |
| Specialist agents | Static role catalog has several genuine read adapters: `lib/agents/real.ts` | `PARTIAL` | Versioned roles with bounded capabilities and reliable task execution | Many agents are status checks or planned lanes | `EXTEND` |
| Agent delegation | `parentId` is display data only: `lib/hierarchy.ts:21-31` | `NOT PRESENT` | Durable child tasks, recipient contract, return evidence | No delegation execution path | `EXTEND` |
| Agent hierarchy | Organization tree and department grouping render: `app/org/page.tsx`, `lib/hierarchy.ts` | `DEMO / SEEDED` | Reporting relationships linked explicitly to policy and escalation | Hierarchy does not affect runtime | `REFACTOR` |
| Persistent tasks | Task CRUD persists in SQLite: `lib/db.ts:679-739`, `app/api/agents/work/route.ts` | `PARTIAL` | Executable state, owner, attempts, artifacts, deadlines, leases | Current schema is status metadata | `EXTEND` |
| Task dependencies | No dependency schema or scheduler found | `NOT PRESENT` | Required/optional edges and blocked-state evaluation | Entire capability missing | `EXTEND` |
| Approval queue | No approval entity, route, or enforcement found | `NOT PRESENT` | Exact-action approval with scope, approver, expiry, and consumption | Entire capability missing | `EXTEND` |
| Decision log | No decision entity found | `NOT PRESENT` | Options, evidence, decision, owner, review date, result | Entire capability missing | `EXTEND` |
| Shared company knowledge | GBrain adapter and brain dump exist: `lib/connectors/gbrain.ts`, `lib/brain-dump.ts` | `PARTIAL` | Persistent, cited, governed company retrieval | Live GBrain unavailable and governance absent | `CONFIGURE` |
| Private departmental knowledge | No retrieval visibility boundary found | `NOT PRESENT` | Company/department/private namespaces enforced server-side | Entire capability missing | `EXTEND` |
| HR isolation | HR is a visual department only: `app/org/page.tsx`, `lib/schemas.ts:17-32` | `NOT PRESENT` | HR records inaccessible without explicit role and purpose | No data policy or separate namespace | `EXTEND` |
| Finance isolation | Finance is a visual department only: `app/org/page.tsx`, `lib/schemas.ts:17-32` | `NOT PRESENT` | Finance records and tools restricted by identity and action | No data/tool boundary | `EXTEND` |
| Connector permissions | Some chat tools are hardcoded per agent: `lib/agents/real.ts`, `lib/agents/chat.ts` | `PARTIAL` | Server policy for actor, agent, scope, resource, and action level | Allowlist lacks contextual authorization | `REFACTOR` |
| Agent identity | Static runtime IDs and metadata exist: `lib/agents/runtime.ts`, `lib/schemas.ts:17-32` | `PARTIAL` | Versioned principal with company, role, policy, owner, and provenance | Agent ID is not a security principal | `EXTEND` |
| Audit trail | Messages, final runs, and broadcasts persist: `lib/agents/chat.ts`, `lib/agents/runtime.ts` | `PARTIAL` | Append-only event timeline for intent, policy, tools, actions, and transitions | Cannot reconstruct a complete action | `EXTEND` |
| Workflow recovery | No checkpoints, leases, or resumable run state found | `NOT PRESENT` | Resume after process/provider failure | Entire capability missing | `EXTEND` |
| Idempotency | Only narrow provider-ID upsert behavior exists: `app/api/webhooks/manychat/route.ts` | `NOT PRESENT` | Stable action key and cached result for every mutation | Retried writes can duplicate | `EXTEND` |
| External integrations | Multiple real adapters exist, but local statuses were unconfigured: `lib/connectors/index.ts` | `PARTIAL` | Health-tested read/write contracts with least privilege | Configuration, policy, and live proof incomplete | `CONFIGURE` |
| GitHub integration | No product issue/PR connector found | `NOT PRESENT` | Scoped issue/PR operations only if MVP later needs them | No implementation or immediate MVP proof need | `INVESTIGATE` |
| Cost tracking | No token, model cost, or budget record found | `NOT PRESENT` | Per-run/task/company usage and circuit breakers | Entire capability missing | `EXTEND` |
| Observability | Connector statuses and final run rows exist: `lib/connectors/index.ts`, `lib/analytics.ts` | `PARTIAL` | Trace IDs, spans, metrics, SLOs, alerts, and cost | No end-to-end trace or recovery signal | `EXTEND` |
| Skills architecture | Disk skills are readable and operator cards are seeded: `lib/skills-catalog.ts`, `app/skills/page.tsx`, `lib/sop-playbooks.ts:707-742` | `DEMO / SEEDED` | Versioned runtime skill contracts tied to agent policy | Catalog/docs are not runtime dispatch | `REFACTOR` |
| Analytics | Real run volume is combined with snapshots and seeded metrics: `app/analytics/page.tsx`, `lib/operating-metrics.ts`, `lib/seed.ts:1021-1025` | `PARTIAL` | Verified operational, quality, latency, cost, and failure metrics | Mixed evidence quality and no SLOs | `EXTEND` |
| Automated testing | 963 tests pass: `vitest.config.ts`, `tests/` | `REAL` | Unit, integration, E2E, policy, recovery, concurrency, live sandbox | System-critical classes missing | `EXTEND` |
| Production security | Optional shared token and local secret handling: `middleware.ts`, `lib/creds.ts` | `PARTIAL` | Identity, authorization, isolation, secret manager, policy enforcement | Current gate grants shared full authority | `REPLACE` |
| Production deployment | Build passes; no deploy or CI manifest found | `PARTIAL` | Reproducible deploy, migration, health, backup, rollback | Source cannot prove an operating production path | `EXTEND` |
| Optimal Engine claims | Documented pipeline has no matching runtime: `README.md:109-127` | `NOT PRESENT` | Either implement governed facts later or label as vision | Copy can overstate current capability | `REMOVE` |

## 15. Framework Comparison

### 15.1 Decision criteria

Framework options were assessed against the smallest credible MVP, TypeScript fit, durable task state, resumability, human approval, policy enforcement, observability, integration effort, migration risk, and overlap with the existing FounderOS UI/control-plane concepts.

| Option | Fit | Main benefit | Main cost or conflict | Recommendation |
|---|---|---|---|---|
| A. Existing stack only, no control-plane extensions | Low | No new dependency | Cannot satisfy durable delegation, approval, recovery, or authorization | `NOT NEEDED` |
| B. Existing stack plus targeted TypeScript extensions | High | Preserves working UI, schemas, routes, and adapter investment | Requires careful execution and policy design | `RECOMMENDED NOW` |
| C. Paperclip | Medium to high later | Strong company control-plane concepts: org, tasks, budgets, governance, approvals, audit | Large overlap with FounderOS product surface and migration risk | `POSSIBLY LATER` |
| D. OpenClaw | Medium later | Self-hosted gateway, channel connectivity, isolated agents/workspaces, session routing | Does not replace the company task and policy control plane | `POSSIBLY LATER` |
| E. OpenAI Agents SDK | Medium later | Agents, tools, handoffs, sessions, guardrails, tracing, resumable runs and tool approval patterns | Adds a runtime abstraction before FounderOS has stable policy/task contracts | `POSSIBLY LATER` |
| F. LangGraph | Low for MVP | Durable graph checkpoints and interrupt-based human review | Python/graph runtime duplicates the current TypeScript operating model | `NOT NEEDED` |

### 15.2 Source basis

- Paperclip describes itself as an open-source orchestration control plane for zero-human companies, including roles, tasks, budgets, governance, approvals, audit, persistent state, and multi-company isolation. It also states that it manages agents rather than being an agent framework. See [Paperclip repository](https://github.com/paperclipai/paperclip) and [Paperclip documentation](https://docs.paperclip.ing/).
- OpenClaw is a self-hosted gateway with channels, sessions, memory, and multi-agent routing, including isolated workspaces and bindings. See [OpenClaw documentation](https://docs.openclaw.ai/) and [multi-agent routing](https://docs.openclaw.ai/concepts/multi-agent).
- The OpenAI Agents SDK documents agents, tools, handoffs, manager patterns, guardrails, sessions, tracing, resumable state, and local tool approval. See [Agents SDK concepts](https://openai.github.io/openai-agents-python/agents/) and [Agents SDK running agents](https://openai.github.io/openai-agents-js/guides/running-agents/).
- LangGraph documents persisted checkpoints and interrupt-based human-in-the-loop approval, edit, and rejection. See [LangGraph persistence](https://docs.langchain.com/oss/python/langgraph/persistence) and [human in the loop](https://docs.langchain.com/oss/python/langchain/human-in-the-loop).

These official feature descriptions establish capability, not automatic suitability. The recommendation is an architectural inference based on overlap and migration cost in this repository.

### 15.3 Recommended decision

Choose option B now. Build the missing contracts in the existing TypeScript system before choosing a deeper framework:

1. Durable task and run state machine
2. Agent identity and versioned policy
3. Central action executor
4. Approval binding
5. Idempotency and recovery
6. Scoped conversations and knowledge
7. Append-only event trail

After the MVP passes its acceptance tests, run a bounded Paperclip comparison using the same scenario and data contracts. A later framework should earn adoption by reducing code and operational risk, not by duplicating the current UI under another name.

## 16. Recommended Target Architecture

### 16.1 Smallest credible production shape

```text
Founder UI
  -> Authenticated API
  -> Command service
       -> Task and workflow state machine
       -> Agent registry and policy service
       -> Approval service
       -> Durable run queue and workers
       -> Action executor with idempotency
       -> Knowledge service and GBrain adapter
       -> Decision and artifact store
       -> Append-only event ledger
  -> Read/write connector adapters
  -> PostgreSQL, object storage, secret manager, telemetry backend
```

### 16.2 Component responsibilities

1. **UI shell:** Keep existing navigation and visual language. Add scoped conversations, plan view, task dependencies, approval inbox, and run evidence.
2. **Authenticated API:** Establish real user/company identity and resource scopes at request entry.
3. **Command service:** Validate commands and create durable intent records before any work starts.
4. **Agent registry:** Persist versioned role, instructions, model policy, allowed capabilities, department, and escalation route.
5. **Organization model:** Reference policy and ownership. It must not silently infer permissions from visual hierarchy.
6. **Task engine:** Model parent, dependency, state, owner, attempt, lease, heartbeat, deadline, input, output, and failure class.
7. **Workflow engine:** Expand a versioned workflow into tasks and dependencies, then react to durable state changes.
8. **Worker runtime:** Claim jobs atomically, heartbeat, checkpoint, release leases, and resume safely.
9. **Policy engine:** Deny by default. Evaluate actor, agent, company, department, tool, resource, data sensitivity, and action level.
10. **Approval service:** Bind approval to an immutable action digest, scope, approver, expiry, and one-time consumption.
11. **Action executor:** Be the only path to external mutation. Enforce policy, approval, idempotency, rate limit, audit, and response capture.
12. **Connector adapters:** Separate read and write capabilities. Expose normalized typed contracts and provider-specific evidence.
13. **Knowledge service:** Retrieve company-scoped sources, preserve citations, mark trust/provenance, and isolate private namespaces.
14. **Decision store:** Record question, options, evidence, decision, owner, timestamp, review date, and observed result.
15. **Event ledger:** Append actor, intent, policy decision, tool call, action digest, provider response, and state transition.
16. **Production persistence:** Use PostgreSQL for concurrent durable state. Keep SQLite for local development and memory tests.
17. **Operations:** Add trace IDs, structured logs, model/provider usage, cost budgets, metrics, alerts, backups, migrations, and rollback.

### 16.3 Action levels

| Level | Example | Default policy |
|---|---|---|
| L0 | Read-only analysis | Allow only within identity, department, resource, and data scope; log access |
| L1 | Internal draft | Allow within scope; mark as draft and prevent external delivery |
| L2 | External communication | Explicit human approval bound to recipient, channel, and exact content |
| L3 | External system modification | Explicit human approval bound to system, record, operation, and exact parameters |
| L4 | Financial or legal commitment | Explicit named approver, stronger authentication, limits, and exact-action binding |

## 17. MVP Definition

### 17.1 Roles

- Human Founder/CEO
- Chief of Staff agent
- Research and Marketing agent
- Operations agent
- HR agent, invoked only when the plan requires people or policy work

### 17.2 Scenario

Founder request: "Prepare the launch plan for Product X."

Expected execution:

1. API creates one company-scoped conversation, root task, and run.
2. Chief of Staff reads permitted context and produces a proposed plan.
3. Runtime creates durable child tasks for market evidence and launch operations.
4. Dependencies require research evidence before final positioning and launch synthesis.
5. Agents claim tasks through leases, save checkpoints, and attach source-backed artifacts.
6. Chief of Staff validates required outputs and consolidates one launch plan.
7. Any external communication remains a draft unless an L2 approval is granted.
8. Founder accepts, revises, or rejects the final plan.
9. Every transition, tool call, approval, artifact, and decision remains queryable.

### 17.3 MVP proof obligations

The MVP is successful only if it proves:

- Direct agent chat is scoped to one company, user, and conversation.
- Chief of Staff creates and monitors durable delegated tasks.
- Dependencies block downstream work correctly.
- A killed worker resumes from persisted state without duplicate external action.
- Knowledge answers include retrievable source references.
- Department-private knowledge cannot be retrieved outside policy.
- L2, L3, and L4 actions cannot execute without a matching unexpired approval.
- Editing an approved payload invalidates the approval.
- Duplicate delivery attempts reuse the idempotency result.
- A complete audit timeline reconstructs the outcome and cost.

## 18. Risks

| ID | Risk | Severity | Evidence | Mitigation |
|---|---|---:|---|---|
| R-01 | External message sent without informed approval | Critical | Direct reply and DM routes | Central action executor and L2 approvals |
| R-02 | Shared token grants full system authority | Critical | Optional gate, no authorization | User identity, sessions, scoped policy |
| R-03 | Cross-user/company chat data mixing | Critical | History keyed only by agent ID | Company/user/conversation foreign keys and policy |
| R-04 | Duplicate external actions after retry | Critical | No general idempotency | Stable action digest and provider idempotency |
| R-05 | Runtime failure loses task progress | High | Synchronous calls, no checkpoint | Durable runs, leases, heartbeat, checkpoint |
| R-06 | UI overstates autonomous behavior | High | Task/workflow labels exceed runtime | Align copy with verified maturity and evidence |
| R-07 | Prompt injection triggers unsafe behavior | High | Untrusted connector content, prompt-only read policy | Trust labels, tool policy, output controls, adversarial tests |
| R-08 | Knowledge returns stale or conflicting facts | High | No fact governance | Provenance, confidence, conflict, review, expiry |
| R-09 | Dependency vulnerabilities are reachable | High | 18 audit findings | Upgrade plan, reachability review, rebuild, retest |
| R-10 | Unsupported Node runtime breaks installation | Medium | Node 26 failure, Node 22 success | Pin Node 22 and enforce engines/CI |
| R-11 | GBrain outage silently changes answer quality | High | Current error and local fallback | Explicit degraded mode, retrieval telemetry, result labels |
| R-12 | Connector status overstates readiness | Medium | Some checks only test key presence | Multi-stage readiness states and sandbox contracts |
| R-13 | SQLite contention or corruption under workers | High | Single-file production persistence | PostgreSQL and migration/backup tests |
| R-14 | No deploy/rollback evidence | High | No CI/deployment manifests | Reproducible pipeline, migrations, health and rollback |
| R-15 | Model/provider spend is unbounded | High | No token/cost budgets | Per-company/task budget and circuit breaker |

## 19. Recommended Development Phases

### Phase 0: Trustworthy baseline

- Pin Node 22 and add CI for install, test, typecheck, build, and audit review.
- Triage direct dependency advisories.
- Correct UI/docs claims that imply unavailable autonomy.
- Establish company, user, conversation, and request identity schemas.
- Define action levels and deny-by-default policy contract.

Exit: every current capability has honest labeling, CI is repeatable, and security boundaries have approved contracts.

### Phase 1: Durable single-agent execution

- Add task, run, attempt, event, artifact, and checkpoint schemas.
- Implement atomic claim/lease/heartbeat and crash recovery.
- Scope chat history and knowledge retrieval.
- Add structured tracing and cost capture.

Exit: one agent completes a durable read-only task and resumes after a forced worker interruption.

### Phase 2: Governed actions

- Split connector read/write capabilities.
- Route all writes through the action executor.
- Implement policy decisions, approvals, payload digesting, expiry, and idempotency.
- Add sandbox contract tests for email and one social provider.

Exit: no L2, L3, or L4 action can bypass policy or execute twice.

### Phase 3: Multi-agent MVP

- Implement Chief of Staff planning and durable child-task delegation.
- Add dependencies, consolidation, evidence requirements, and escalation.
- Run the Product X scenario with Research/Marketing and Operations agents.

Exit: all MVP proof obligations in section 18 pass.

### Phase 4: Knowledge governance and production operations

- Add source trust, claim/fact/decision records, conflict handling, visibility, review, and expiry.
- Move production persistence to PostgreSQL.
- Add deployment, backup, restore, migration, alert, and rollback evidence.
- Compare the stable contracts with Paperclip and the OpenAI Agents SDK.

Exit: a production readiness review can reconstruct security, reliability, data, cost, and recovery evidence.

## 20. Proposed Next Tasks

No issues or pull requests were created. The following is a proposed implementation backlog only.

| ID | Objective | Why | Depends on | Likely modules | Acceptance criteria | Verification environment |
|---|---|---|---|---|---|---|
| FOS-001 | Pin supported Node runtime | Current documented range includes a failing install | None | `package.json`, runtime version file, README | Clean install/test/build succeeds on pinned runtime; unsupported runtime fails clearly | Fresh local and CI |
| FOS-002 | Add baseline CI | No automated quality gate | FOS-001 | `.github/workflows`, package scripts | Install, tests, typecheck, build run on every PR | GitHub Actions |
| FOS-003 | Triage dependency advisories | Critical/high findings block trust | FOS-001 | `package.json`, lockfile, impacted adapters | Reviewed upgrades land with green tests/build and documented residual risk | Local and CI |
| FOS-004 | Add identity and company scope | Current gate has no user/resource identity | None | schemas, DB repositories, middleware/auth routes | Every request has authenticated user and company scope; negative tests pass | Unit, route integration, browser E2E |
| FOS-005 | Define agent policy contract | Agent metadata is not enforceable authority | FOS-004 | schemas, agent registry, policy module | Versioned allowed capabilities and scopes validate; default is deny | Unit and integration |
| FOS-006 | Scope conversations | Agent-global history can mix data | FOS-004 | chat schema/repo/route/UI | History queries require company, user, and conversation; cross-scope tests deny | Repository and route integration |
| FOS-007 | Add durable task model | Current task rows cannot execute reliably | FOS-004 | schemas, DB, task routes | Parent/dependency/input/output/policy fields persist and validate | Repository tests |
| FOS-008 | Add run and attempt state machine | Runtime has final result only | FOS-007 | runtime, schemas, DB | Legal transitions enforced; failure reason and attempt history retained | Unit and DB integration |
| FOS-009 | Implement job claim and lease | Multiple workers need safe ownership | FOS-008 | worker/runtime, DB | Atomic claim, heartbeat, lease expiry, and single active owner pass concurrency tests | Integration with production DB candidate |
| FOS-010 | Implement checkpoints and resume | A crash currently loses orchestration state | FOS-008, FOS-009 | runtime, model adapter, DB | Forced termination resumes from last checkpoint without restarting completed steps | Process-level recovery test |
| FOS-011 | Add append-only event ledger | Current records cannot reconstruct actions | FOS-004, FOS-008 | schemas, DB, middleware, runtime | Every state transition/tool/action records actor, trace, timestamp, payload digest | Integration and audit replay |
| FOS-012 | Add token/cost accounting | Spend is unbounded and invisible | FOS-008, FOS-011 | LLM adapter, DB, dashboard | Model usage/cost attaches to run/task/company and enforces a test budget | Unit with fixtures, gateway sandbox |
| FOS-013 | Split connector capabilities | Read and write authority is not explicit | FOS-005 | connector types and adapters | Every adapter declares typed read/write operations and required action level | Contract tests |
| FOS-014 | Build central action executor | Write routes bypass governance | FOS-005, FOS-011, FOS-013 | new action service, mutation routes | Direct connector writes outside executor fail tests; executor records policy evidence | Integration |
| FOS-015 | Implement idempotency | Retries can duplicate external actions | FOS-014 | action service, DB, connectors | Same action key returns first result; changed payload requires new key/approval | Concurrency and provider sandbox |
| FOS-016 | Implement approvals | High-impact actions lack human control | FOS-004, FOS-014 | schemas, DB, approval API/UI | Approval binds actor, digest, scope, expiry; edits invalidate; one-time consume enforced | Unit, route integration, E2E |
| FOS-017 | Migrate direct communication writes | Email/Slack/DM routes bypass controls | FOS-014 to FOS-016 | comms/social routes and connectors | L2 sends require matching approval and produce one audit/action record | Email/Slack/social sandboxes |
| FOS-018 | Add connector readiness stages | Key presence can appear operational | FOS-013 | connector index/status UI | Status distinguishes adapter, configured, health, read-tested, write-approved | Unit and sandbox integration |
| FOS-019 | Add knowledge provenance schema | Current source strings cannot govern truth | FOS-004, FOS-011 | brain schemas/repo/GBrain adapter | Retrieval returns source ID, location, trust, visibility, timestamp, citation | Unit and GBrain sandbox |
| FOS-020 | Add knowledge isolation | Departments currently share all data | FOS-005, FOS-019 | policy, retrieval, DB | Cross-department/private queries are denied and logged | Adversarial integration |
| FOS-021 | Add claim/fact/decision governance | Optimal Engine is documentation only | FOS-019, FOS-020 | new knowledge entities/UI | Claims can conflict; facts require validation; decisions record evidence and review | Unit, integration, E2E |
| FOS-022 | Implement Chief of Staff planner | Current conductor selects one agent | FOS-007 to FOS-012 | conductor/runtime/task service | Request creates reviewable plan and durable child tasks with evidence requirements | Scenario integration |
| FOS-023 | Add dependency scheduler | No task ordering exists | FOS-009, FOS-022 | task engine/worker | Downstream tasks remain blocked until all required dependencies succeed | Deterministic integration |
| FOS-024 | Add consolidation and escalation | Fan-out has no managed outcome | FOS-022, FOS-023 | conductor/runtime/artifacts | Chief validates child outputs, creates synthesis, escalates missing/failed evidence | Scenario integration |
| FOS-025 | Align org model with policy references | Visual hierarchy is mistaken for authority | FOS-005 | org page, hierarchy, agent registry | UI distinguishes reporting line from granted capability; policy links are visible | Component and E2E |
| FOS-026 | Add browser E2E suite | Current tests do not prove user journeys | FOS-004, FOS-016, FOS-024 | test harness | Auth, scoped chat, delegation, approval, recovery journey passes | Isolated E2E |
| FOS-027 | Add prompt-injection test suite | External content is untrusted | FOS-005, FOS-013, FOS-020 | agents, connectors, policy tests | Malicious source text cannot change policy or invoke unauthorized tool | Adversarial integration |
| FOS-028 | Select production database | SQLite is unsuitable for intended workers | FOS-007 to FOS-011 | DB abstraction, migrations | PostgreSQL concurrency, migration, backup/restore tests pass | Ephemeral PostgreSQL and staging |
| FOS-029 | Add deployment and rollback | No source-verifiable deployment | FOS-002, FOS-003, FOS-028 | deployment manifests/runbook | Staging deploy, health, migration, rollback, restore are exercised | Staging |
| FOS-030 | Run framework benchmark | Avoid premature framework migration | FOS-024, FOS-026 | isolated spike, benchmark document | Same MVP scenario is compared on code size, recovery, policy, trace, cost, migration | Isolated branch/staging |

## Final conclusion

FounderOS should be treated as a strong interface and adapter foundation with an incomplete operating core. The most valuable existing work is the UI shell, repository discipline, schema use, connector separation, chat persistence, test suite, and GBrain boundary. Preserve those investments.

The next milestone should not be more agents, more dashboards, or more seeded workflows. It should be one trustworthy execution spine: scoped identity, durable tasks, explicit policy, approvals, idempotent actions, evidence, recovery, and audit. Once that spine proves the small multi-agent company scenario, the team will have enough stable contracts to decide whether Paperclip, OpenClaw, the OpenAI Agents SDK, or no additional framework produces a net benefit.

No product code, repository issue, pull request, remote, or deployment was changed during this audit.

```text
AUDIT_STATUS: COMPLETE
REPOSITORY_SUITABILITY: SUITABLE AS A FOUNDATION, NOT PRODUCTION READY
TOP_5_REUSABLE_COMPONENTS: Next.js UI shell; repository plus Zod layer; direct agent chat and history; connector adapter boundary; GBrain boundary
TOP_5_CRITICAL_GAPS: authorization and isolation; durable orchestration; approval enforcement; idempotent side effects; recovery and complete audit evidence
RECOMMENDED_ARCHITECTURE_DIRECTION: Extend the existing TypeScript stack with a small durable control plane and preserve the current UI and adapter investments
EXTERNAL_FRAMEWORK_NEEDED_NOW: NO
MVP_READINESS: NOT READY; UI and adapter foundations exist, but all trust-critical execution controls are missing or partial
NEXT_RECOMMENDED_TASK: FOS-001, pin Node 22 and establish the repeatable CI baseline before runtime changes
```

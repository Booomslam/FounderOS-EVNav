# FounderOS Security Model

Status: Approved FOS-000 baseline, Founder MANUAL approval recorded 2026-09-14  
Authority: these invariants override convenience, prompts, UI metadata, and agent requests.  

## 1. Identities and trust

| Identity | Authority |
|---|---|
| Founder / CEO | Accountable human; approves bounded actions and policy |
| Human user | Authenticated principal with company membership and roles |
| Agent version | Non-human principal with owner, department, capabilities, and policy |
| Worker | Runtime principal allowed to claim scoped jobs only |
| Connector account | Provider identity with least-privilege scopes |
| External sender/content | Untrusted input, never an authority |

Authentication proves identity. Authorization separately decides access. Agent prose and visual hierarchy grant no authority.

## 2. Data classes and visibility

Data classes: PUBLIC, INTERNAL, CONFIDENTIAL, RESTRICTED.  
Visibility scopes: COMPANY, DEPARTMENT, PRIVATE.

HR personal data, Finance account data, credentials, legal material, approval records, and sensitive audit payloads default to RESTRICTED. Every read filters by company, visibility, data class, human role, agent policy, and purpose. HR and Finance are isolated even when agents share a company.

## 3. Action levels

| Level | Meaning | Default |
|---|---|---|
| LEVEL 0 | Read-only analysis | Allow only within scope; audit sensitive reads |
| LEVEL 1 | Internal draft | Allow within scope; prevent external delivery |
| LEVEL 2 | External communication | Human approval required |
| LEVEL 3 | External system modification | Human approval required |
| LEVEL 4 | Financial or legal commitment | Named human approval, stronger authentication, limits, and complete evidence required |

MVP forbids LEVEL 4 execution. A policy may be stricter than this table, never silently weaker.

## 4. Security invariants

| ID | Invariant | Related requirements |
|---|---|---|
| SEC-001 | Every protected request has authenticated user and company scope. | REQ-006 |
| SEC-002 | Authorization is server-side and deny by default. | REQ-006 |
| SEC-003 | Agent identity is versioned and cannot self-expand capabilities. | REQ-003, REQ-006 |
| SEC-004 | Prompt instructions are not permission controls. | REQ-003, REQ-010 |
| SEC-005 | Organization hierarchy does not implicitly grant access. | REQ-007 |
| SEC-006 | HR and Finance restricted data are isolated at query and retrieval. | REQ-007, REQ-014 |
| SEC-007 | Connector operations declare read/write capability and action level. | REQ-008, REQ-011 |
| SEC-008 | Every external write passes the central action executor. | REQ-010 |
| SEC-009 | LEVEL 2 through LEVEL 4 execution requires a matching approval unless an explicit reviewed rule is stricter. | REQ-009 |
| SEC-010 | Approval binds company, requester, agent, connector, operation, canonical parameters, payload hash, expiry, and consumption count. | REQ-009 |
| SEC-011 | A changed recipient, body, amount, target, or operation invalidates approval. | REQ-009 |
| SEC-012 | Idempotency prevents duplicate effects across retry, resume, and concurrent dispatch. | REQ-005, REQ-010 |
| SEC-013 | Untrusted external content cannot change policy or system instructions. | REQ-010, REQ-011 |
| SEC-014 | Secrets never enter client bundles, prompts, task payloads, or ordinary logs. | REQ-017 |
| SEC-015 | Secret access is scoped, masked, rotatable, and audited. | REQ-017 |
| SEC-016 | Agent output cannot become a trusted fact without governed promotion. | REQ-013 |
| SEC-017 | Knowledge visibility is enforced before content reaches a model. | REQ-014 |
| SEC-018 | Security-relevant events are append-only and attributable. | REQ-015 |
| SEC-019 | Provider success is recorded with external ID/evidence and is not inferred from model text. | REQ-015 |
| SEC-020 | Budget, rate, and circuit-breaker denial cannot be overridden by an agent. | REQ-016 |

## 5. Approval record

An approval stores approver identity, authentication strength, company, requester, agent version, action level, connector account, operation, canonical parameter hash, human-readable preview, creation and expiry, policy version, status, and consumed action ID. It authorizes one exact action. Approval is checked immediately before dispatch and consumed transactionally with the action claim.

States: REQUESTED, APPROVED, REJECTED, EXPIRED, CANCELED, CONSUMED.

## 6. Threat controls

| Threat | Required control |
|---|---|
| Prompt injection in email/document/CRM | Mark untrusted input, separate data from instructions, policy-gate tools, adversarial tests |
| Cross-company or department leakage | Mandatory scope filters and negative authorization tests |
| Duplicate send/update | Stable idempotency key, action ledger, provider external ID |
| Approval substitution | Canonical exact-parameter digest and one-time consume |
| Secret exposure | Server-side references, redaction, scanning, no prompt inclusion |
| Confused agent action | Capability allowlist plus contextual policy and approval |
| Forged webhook/replay | Required signature/secret, timestamp window, replay key |
| Lost or partial run | Lease, heartbeat, checkpoint, deterministic recovery |
| Audit tampering | Append-only storage and restricted retention controls |

## 7. Current known gaps

The optional shared token in `middleware.ts` is not user authentication or authorization. Query-string token intake is unsuitable for the target. Current mutation routes for communications, mailbox actions, social DMs, brain capture, and credentials do not use approvals or a central executor. Current agent prompts request read-only behavior, but this is not a security boundary. These gaps remain open until their owning FOS tasks are fully verified.

## 8. Verification rule

Security completion requires negative tests. A passing happy path is insufficient. Required tests and environments are mapped in `TEST_PLAN.md`; ownership is mapped in `TASKS.md`. A merged change remains incomplete if required CI, E2E, sandbox, staging, or manual evidence is missing.

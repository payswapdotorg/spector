# Spector — Frozen Architecture

**Status:** FROZEN v1.0  
**Date:** 2026-09-12  
**Repository:** `payswapdotorg/spector`

## 1. Product boundary

Spector is the **Legal Work Operating System**. It is not a generic legal chatbot, generic RAG product, or single autonomous lawyer agent.

The canonical unit of work is a **Matter-scoped Work Item** executed against a permissioned legal-work graph and producing inspectable artifacts.

The system must:

- preserve the firm/client/matter context that caused work to exist;
- make consequential claims traceable to immutable source versions and precise locators;
- execute bounded, durable workflows with explicit budgets and approvals;
- learn firm-specific knowledge, workflows, and outcomes without crossing tenants or ethical walls;
- remain model-provider neutral;
- treat external legal authorities as pluggable capabilities rather than the system boundary;
- support a governed third-party/developer app ecosystem through the App Marketplace.

## 2. Architectural spine

The **Legal Work Graph** is the canonical spine.

```text
People / Organizations / Clients
             │
             ▼
          Matters
             │
             ▼
        Legal Work Graph
      ┌──────┼────────┐
      ▼      ▼        ▼
 Knowledge  Work     Outcomes
      │      │        │
      └──────┼────────┘
             ▼
        Case Brain / Firm Brain
             │
             ▼
        Work Compiler
             │
             ▼
        AI Workforce
             │
             ▼
       Evidence + Verification
             │
             ▼
        Artifacts / Approval
             │
             ▼
       Outcome / Learning Loop
```

Case Brain and Firm Brain are **materialized intelligence views over the Legal Work Graph**, not independent competing databases of truth.

## 3. Layers

### 3.1 Experience Layer

Matter Workspace, Case Brain, Legal Inbox, Research, Review, Drafting, Workflow Builder, Firm Command Center, Admin/Policy Center, Developer Console, Marketplace.

### 3.2 Trust & Policy Layer

Tenant isolation, RBAC/ABAC, matter ACLs, ethical walls, purpose-of-use policy, DLP, retention/legal hold, approval policy, model/data policy, extension permissions, audit.

Authorization is evaluated **before retrieval and before tool execution**.

### 3.3 Matter & Work Layer

Organization, User, Team, Client, Matter, Party, Work Item, Workflow, Agent Run, Artifact, Approval, Outcome.

Every operational action is attributable to an organization and, when matter-bound, to a Matter + Work Item.

### 3.4 Knowledge Plane

Knowledge Source → Source Version → Document → Section/Span → Claim → Evidence Link → Authority/Legal Knowledge.

Knowledge classes are explicitly separated:

1. **Authority** — statutes, regulations, cases, official material, licensed legal research sources.
2. **Matter evidence** — client documents, correspondence, facts, produced discovery.
3. **Firm knowledge** — templates, precedents, playbooks, approved workflows, examples.
4. **Open web** — lowest-trust external information.

### 3.5 Legal Work Engine

Work Compiler converts a legal objective into a typed executable graph:

`Objective → Ontology/Context → Work Plan → Typed Tasks → Tools/Agents → Evidence Requirements → Verification → Approval → Artifact`.

No prompt-only workflow is canonical.

### 3.6 Model & Tool Plane

Provider-neutral Model Gateway and Tool Registry. Routing uses task type, risk, data policy, quality target, cost, latency, and fallback policy.

Models never receive broader data access than the enclosing task's authorization scope.

### 3.7 Evidence & Verification Plane

Citation verification, quote verification, authority verification, temporal validity, completeness, contradiction/consistency checks, policy checks, draft checks. Outputs carry an **Evidence Report**.

### 3.8 Platform & Ecosystem Plane

Connector framework, Developer SDK, App Registry, App Marketplace, App Installations, extension permissions, sandbox/runtime boundary, app lifecycle, app telemetry and trust signals.

## 4. Canonical infrastructure

Initial implementation choices are deliberately boring and replaceable:

- **Application:** Next.js 16 + TypeScript.
- **Canonical database:** PostgreSQL (Neon-compatible).
- **Object storage:** S3-compatible immutable object storage.
- **Search:** OpenSearch/Elasticsearch-class hybrid lexical + vector retrieval.
- **Graph:** PostgreSQL relational graph tables/projections first; dedicated graph DB only if measured need emerges.
- **Durable workflows:** Temporal-class workflow runtime; no request/response process may own long-running legal execution.
- **Queue/cache:** durable queue + Redis-class cache where justified.
- **Observability:** OpenTelemetry-compatible traces, metrics and structured audit logs.
- **Model access:** provider-neutral gateway; no vendor SDK allowed to become a domain abstraction.

## 5. Core execution rules

### Retrieval

`intent + jurisdiction + date + matter scope → lexical/semantic/metadata retrieval → graph/citation expansion → rerank → authority/temporal filtering → contradiction scan → EvidencePacket`.

### Ingestion

`malware scan → immutable storage → fingerprint/dedupe → layout-aware parse/OCR → structural segmentation → metadata/ACL classification → indexing → graph extraction → quality gates → SourceVersion`.

### Agent contract

Every agent declares: tools, data scopes, max iterations, model policy, output schema, escalation policy, evaluator, and budget.

Agents are bounded workers, not free-form autonomous lawyers.

### Durable workflow rules

Checkpointing, idempotency, cancellation, concurrency limits, retries, deterministic state transitions, version pinning, human approval gates, and complete run history are mandatory.

## 6. Security invariants

1. Tenant isolation is a system invariant, not a UI convention.
2. Authorization occurs before retrieval.
3. Authorization occurs before tool execution.
4. Matter/ethical-wall rules are enforced server-side.
5. Source versions are immutable.
6. Audit records are append-only and tamper-evident.
7. Model providers do not define data ownership or retention semantics.
8. Extension apps execute only within explicitly granted scopes.
9. Marketplace packages are versioned, signed/hashed, scanned, and revocable.
10. No app receives unrestricted database credentials.

## 7. Marketplace boundary

The marketplace exposes **capabilities**, not direct access to internal tables.

An app may declare and request capabilities such as:

- `matter.read`
- `matter.write`
- `work.create`
- `work.read`
- `evidence.read`
- `knowledge.read`
- `document.read`
- `document.write`
- `workflow.invoke`
- `workflow.register`
- `legal.authority.read`
- `client.communication.send`
- `billing.read`
- `analytics.read`
- `events.subscribe`

High-risk scopes require explicit tenant admin approval and may require matter-level consent.

## 8. Non-goals / anti-patterns

Do not implement:

- a single giant vector store as the legal knowledge system;
- a single "AI lawyer" with unrestricted tools;
- citations generated by the frontend;
- an app-specific database that becomes an undisclosed system of record;
- model-provider-specific business logic;
- microservices solely for organizational aesthetics;
- fine-tuning as the primary correctness strategy;
- direct marketplace app access to PostgreSQL;
- marketplace publication without security, permission, version, and provenance metadata.

## 9. Architectural invariants to test

Every release must demonstrate:

- cross-tenant access is denied;
- ethical-wall violations are denied;
- evidence locators remain resolvable after re-indexing;
- replaying a durable task does not duplicate side effects;
- model fallback preserves policy and provenance;
- a revoked app token/capability stops further access;
- uninstalling an app does not delete canonical firm/matter data;
- an app version can be rolled back;
- marketplace metadata cannot mutate canonical legal truth;
- every consequential generated claim can be traced to Evidence Links or is explicitly marked unsupported.

# Spector Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use `superpowers:subagent-driven-development` or `superpowers:executing-plans` to execute task-by-task. Do not improvise architecture from the task title alone; read the frozen docs first.

**Goal:** Implement the Spector Legal Work Operating System from the frozen contracts with three workers operating concurrently wherever the dependency graph permits.

**Architecture:** PostgreSQL is canonical for transactional truth; the Legal Work Graph is a projection over canonical domain relationships; Case Brain/Firm Brain are materialized intelligence views; durable workflows execute bounded tasks; Evidence/Verification supplies provenance; the App Marketplace extends capabilities through scoped APIs and sandboxed apps.

**Tech Stack:** Next.js 16, TypeScript, PostgreSQL/Neon, S3-compatible object storage, OpenSearch-class hybrid search, Temporal-class workflows, Redis-class cache where needed, OpenTelemetry, provider-neutral model gateway.

**Spec:** `docs/architecture/FROZEN_ARCHITECTURE.md`, `docs/architecture/DOMAIN_MODEL.md`, `docs/architecture/APP_MARKETPLACE.md`, `docs/architecture/WORKER_CONTRACTS.md`.

## Global Constraints

- PostgreSQL is the canonical transactional system of record.
- Authorization is checked before retrieval and tool execution.
- Matter scope and ethical walls are enforced server-side.
- EvidenceLink is first-class and references immutable SourceVersions.
- All long-running work uses durable workflow execution.
- No marketplace app receives raw DB credentials.
- Model providers are hidden behind the Model Gateway.
- Agents are bounded by tools, data scopes, budgets, schemas and escalation rules.
- Workflow/app versions are immutable once published.
- GitHub Issues are the authoritative work queue.
- Workers may not change shared contracts without an architecture-change issue and lead approval.

---

## Task A0 — Repository and developer bootstrap
**Owner:** Lead  
**Depends on:** none

Deliver a runnable monorepo/toolchain, CI, formatting/lint/typecheck/test conventions, and package boundaries for `core`, `execution`, `platform`, and shared contracts.

Acceptance: a clean checkout installs, typechecks, runs unit tests, runs contract tests, and produces deterministic CI.

## Task A1 — Canonical persistence schema
**Owner:** Worker A  
**Depends on:** A0

Create PostgreSQL schema for Organization, User, Team, Client, Matter, Party, WorkItem, KnowledgeSource, SourceVersion, Document, EvidenceLink, Workflow, AgentRun, Artifact, Approval, Outcome, App, AppVersion, AppInstallation, Capability, AuditEvent. Add authorization-supporting indexes and migration tests.

## Task A2 — Identity, policy and matter authorization
**Owner:** Worker A  
**Depends on:** A1

Implement organization membership, roles/policies, matter ACLs, ethical-wall constraints, capability authorization and policy evaluation. Test cross-tenant, cross-matter and revoked-capability denials.

## Task A3 — Matter/Work APIs
**Owner:** Worker A  
**Depends on:** A1,A2

Implement typed Matter/WorkItem lifecycle APIs with objective, scope, priority, budget and actor context. Emit canonical work events. Prove authorization and idempotency.

## Task A4 — Knowledge/source/evidence persistence
**Owner:** Worker A  
**Depends on:** A1,A2

Implement source/version/document metadata, immutable versions and EvidenceLink APIs with page/paragraph/table/span/citation locators. Prove integrity and provenance.

## Task A5 — Legal Work Graph
**Owner:** Worker A  
**Depends on:** A3,A4

Build relational graph projections and bounded query APIs for matter, people, knowledge, work, evidence and outcomes. No graph database becomes a new system of record.

## Task A6 — Case Brain / Firm Brain
**Owner:** Worker A  
**Depends on:** A5

Create materialized read models for current matter state, open work, facts, authorities, evidence gaps, decisions, outcomes and approved firm knowledge. Every projection records source event and projection version.

## Task A7 — Audit/event infrastructure
**Owner:** Worker A  
**Depends on:** A1,A2

Implement canonical event envelope, append-only audit records, idempotency keys, correlation/causation IDs and event consumer contracts.

---

## Task B1 — Execution contracts
**Owner:** Worker B  
**Depends on:** A0

Freeze typed contracts for Agent, Tool, WorkflowDefinition/Version, TaskNode, EvidenceRequirement, Evaluator, ApprovalRequest, ModelRoute and AgentRun. Contracts must remain infrastructure-independent.

## Task B2 — Model Gateway
**Owner:** Worker B  
**Depends on:** B1

Implement provider-neutral routing, model metadata, budgets, fallback and redaction hooks. Provider SDKs live only behind adapters.

## Task B3 — Tool Registry and policy bridge
**Owner:** Worker B  
**Depends on:** B1,A2

Register typed tools with required capabilities/data classes. Route every invocation through authorization and audit. Denied tools must never execute.

## Task B4 — Work Compiler
**Owner:** Worker B  
**Depends on:** B1,A3,A5

Compile a legal objective into an inspectable typed work graph with dependencies, evidence requirements, agent/tool scopes, budgets, verification and approval gates. Compilation must not execute work.

## Task B5 — Durable workflow runtime
**Owner:** Worker B  
**Depends on:** B1,B3

Implement Temporal-class durable execution with retries, checkpointing, idempotency, cancellation, concurrency limits, resumability and immutable workflow-version pinning.

## Task B6 — Evidence and verification engine
**Owner:** Worker B  
**Depends on:** A4,A5,B1

Implement citation, quote, authority, temporal, completeness, contradiction and policy verification. Produce Evidence Reports linked to AgentRuns/Artifacts.

## Task B7 — Artifact and approval execution
**Owner:** Worker B  
**Depends on:** B5,B6

Implement versioned artifacts and approval requests/transitions. Policy-defined consequential actions require approval before effect.

## Task B8 — AI Workforce
**Owner:** Worker B  
**Depends on:** B2,B4,B5,B6

Implement bounded specialist agents: Researcher, Document Reviewer, Citation Checker, Chronology Builder, Drafting Agent, Redline Agent, Deposition Analyst, Due Diligence Analyst, Regulatory Monitor, Client Update Agent, Deadline Agent, Knowledge Curator and Quality Controller.

---

## Task C1 — Connector and authority framework
**Owner:** Worker C  
**Depends on:** A2,A4

Implement versioned authority/DMS/email/external-system connector contracts. Connectors produce provenance-preserving SourceVersions and obey Spector authorization.

## Task C2 — Legal Inbox
**Owner:** Worker C  
**Depends on:** A3,A7

Build event-driven inbox projections for changed items, lawyer actions, AI activity, approvals, deadlines and exceptions. Inbox is a projection over canonical Work/Matter state, not a second task database.

## Task C3 — Migration/import engine
**Owner:** Worker C  
**Depends on:** A3,A4

Implement discovery, mapping, validation and provenance-preserving import for matter metadata, documents, templates, workflows, permissions and historical outcomes. Imports are idempotent.

## Task C4 — Benchmark / Prove-It engine
**Owner:** Worker C  
**Depends on:** B6,C3

Run imported historical matters through configurable workflows and compare time, evidence coverage, errors, completion and human-review burden. Results must be reproducible and clearly distinguish measured data from simulation.

## Task C5 — Staffing and firm economics
**Owner:** Worker C  
**Depends on:** A3,A6,A7

Produce projections for lawyer/AI allocation, utilization, matter cost, write-offs, cycle time and recovered capacity. Metrics cite source WorkItems/events and expose assumptions.

## Task C6 — Developer SDK and app manifest
**Owner:** Worker C  
**Depends on:** A0

Create app manifest schema, SDK types, lifecycle hooks, capability declarations and local validation/test harness.

## Task C7 — App Registry and Marketplace
**Owner:** Worker C  
**Depends on:** C6,A2,A7

Implement developer submission, immutable app/version registry, security metadata, review states, listing/search/read APIs and marketplace catalog. Only approved immutable versions are installable.

## Task C8 — App capability/token service
**Owner:** Worker C  
**Depends on:** C6,A2

Issue narrowly scoped capability tokens by organization, app installation, resource class, operation, optional matter and expiry. Support immediate revocation and audit every grant/revoke.

## Task C9 — App runtime gateway/sandbox
**Owner:** Worker C  
**Depends on:** C8,B3

Route app calls through the gateway, enforce declared outbound destinations/capabilities, isolate apps from database internals, and capture telemetry.

## Task C10 — Install/update/revoke lifecycle
**Owner:** Worker C  
**Depends on:** C7,C8,C9

Implement install, configure, enable, update, rollback, disable and uninstall. Uninstall revokes access and preserves canonical data.

## Task C11 — Typed extension points
**Owner:** Worker C  
**Depends on:** C9,B1,B5

Implement versioned extension contracts for command, matter_panel, workflow_node, agent, knowledge_provider, connector, artifact_renderer, event_handler and automation_rule.

## Task C12 — Workflow discovery and outcome learning
**Owner:** Worker C  
**Depends on:** A6,A7,B8

Detect recurring successful work patterns and build a governed learning pipeline that proposes workflow/agent improvements from approved historical outcomes. Published workflows are never auto-mutated; proposals require approval and cite their source outcomes.

## Task C13 — Matter Twin and scenario simulation
**Owner:** Worker C  
**Depends on:** A6,B4,B6,B8

Implement isolated, reproducible scenario simulations against current matter state, assumptions, evidence and approved workflows. Simulations cannot silently mutate canonical Matter/Work state.

---

## Task D1 — API/UI shell
**Owner:** Lead with A/C contributions  
**Depends on:** A3,B4,C2,C7

Build a thin production shell for Matter Workspace, Case Brain, Legal Inbox, Work execution, Evidence Report, Marketplace and Admin/Policy. Every visible action must map to a working backend capability; no dead buttons.

## Task D2 — End-to-end architecture proof slices
**Owner:** Lead + all workers  
**Depends on:** A6,B8,C10,D1,C12,C13

Demonstrate complete flows:

1. Organization/user → Matter → SourceVersion → EvidenceLink → Case Brain.
2. Legal objective → Work Compiler → AI Workforce → Verification → Artifact → Approval → Outcome.
3. Developer → App manifest → validation/security → publication → install → matter-scoped capability → extension invocation → audit → revoke/uninstall.
4. Historical matter import → benchmark/Prove-It report.
5. Outcome → governed workflow improvement proposal → human approval path.
6. Matter Twin scenario → explicit assumptions/evidence → isolated result with no canonical mutation.

---

## Integration gates

A worker can merge independently only when its contract tests pass and its public interfaces match the frozen docs. The tech lead owns integration commits and rejects undocumented cross-lane coupling.

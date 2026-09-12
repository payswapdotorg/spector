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

**Deliverable:** runnable monorepo/toolchain, CI, formatting/lint/typecheck/test conventions, package boundaries for `core`, `execution`, `platform`, and shared contracts.

**Acceptance:** a clean checkout installs, typechecks, runs unit tests, runs contract tests, and produces a deterministic CI result.

## Task A1 — Canonical persistence schema

**Owner:** Worker A  
**Depends on:** A0

Create the PostgreSQL schema for Organization, User, Team, Client, Matter, Party, WorkItem, KnowledgeSource, SourceVersion, Document, EvidenceLink, Workflow, AgentRun, Artifact, Approval, Outcome, App, AppVersion, AppInstallation, Capability, AuditEvent.

Use explicit tenant/matter foreign keys and indexes for authorization predicates. Write migration tests and uniqueness/foreign-key tests.

## Task A2 — Identity, policy and matter authorization

**Owner:** Worker A  
**Depends on:** A1

Implement organization membership, roles/policies, matter ACLs, ethical-wall constraints, capability authorization and policy evaluation. Add deny-by-default tests for cross-tenant, cross-matter and revoked-capability access.

## Task A3 — Matter/Work APIs

**Owner:** Worker A  
**Depends on:** A1,A2

Implement typed APIs/repositories for Matter and WorkItem lifecycle. Work creation must support objective, scope, priority, budget and actor context. Emit canonical work events.

## Task A4 — Knowledge/source/evidence persistence

**Owner:** Worker A  
**Depends on:** A1,A2

Implement source/version/document metadata, immutable versioning and EvidenceLink APIs. Establish locator schemas for page/paragraph/table/span/citation positions. Add integrity and provenance tests.

## Task A5 — Legal Work Graph

**Owner:** Worker A  
**Depends on:** A3,A4

Build relational graph projections and query APIs for matter, people, knowledge, work, evidence and outcome relationships. Provide bounded traversal interfaces instead of leaking graph storage to consumers.

## Task A6 — Case Brain / Firm Brain

**Owner:** Worker A  
**Depends on:** A5

Create materialized read models aggregating current matter state, open work, key facts, authorities, evidence gaps, decisions, outcomes and approved firm knowledge. All derived state must identify source events and projection version.

## Task A7 — Audit/event infrastructure

**Owner:** Worker A  
**Depends on:** A1,A2

Implement canonical event envelope, append-only audit records, idempotency keys, correlation/causation IDs and an internal event consumer contract.

---

## Task B1 — Execution contracts

**Owner:** Worker B  
**Depends on:** A0

Freeze TypeScript contracts for Agent, Tool, WorkflowDefinition, WorkflowVersion, TaskNode, EvidenceRequirement, Evaluator, ApprovalRequest, ModelRoute and AgentRun. These types must not import infrastructure implementation details.

## Task B2 — Model Gateway

**Owner:** Worker B  
**Depends on:** B1

Implement provider-neutral request/response types, routing policy, model metadata, budgets, fallback and redaction hooks. Provider SDKs live only under adapters.

## Task B3 — Tool Registry and policy bridge

**Owner:** Worker B  
**Depends on:** B1,A2

Register typed tools with required capabilities/data classes and route every invocation through authorization and audit. Add contract tests showing denied tools cannot execute.

## Task B4 — Work Compiler

**Owner:** Worker B  
**Depends on:** B1,A3,A5

Implement objective-to-work-graph compilation. Output must be a typed plan with task dependencies, evidence requirements, allowed tools/agents, budgets, verification and approval gates. Plans are inspectable before execution.

## Task B5 — Durable workflow runtime

**Owner:** Worker B  
**Depends on:** B1,B3

Implement workflow execution on a Temporal-class abstraction with retries, checkpointing, idempotency, cancellation, concurrency limits, version pinning and resumability.

## Task B6 — Evidence and verification engine

**Owner:** Worker B  
**Depends on:** A4,A5,B1

Implement citation, quote, authority, temporal, completeness, contradiction and policy verifiers. Produce an Evidence Report attached to AgentRun/Artifact outputs.

## Task B7 — Artifact and approval execution

**Owner:** Worker B  
**Depends on:** B5,B6

Implement versioned artifact lifecycle, approval requests and approved/rejected transitions. Consequential external actions require approval according to policy.

## Task B8 — AI Workforce

**Owner:** Worker B  
**Depends on:** B2,B4,B5,B6

Implement the bounded agent catalog and executor. Initial roles: Researcher, Document Reviewer, Citation Checker, Chronology Builder, Drafting Agent, Redline Agent, Deposition Analyst, Due Diligence Analyst, Regulatory Monitor, Client Update Agent, Deadline Agent, Knowledge Curator and Quality Controller.

---

## Task C1 — Connector and authority framework

**Owner:** Worker C  
**Depends on:** A2,A4 contracts

Implement connector interfaces for authority systems, DMS, email and external systems. Connectors emit provenance-preserving SourceVersions and never bypass Spector authorization.

## Task C2 — Legal Inbox

**Owner:** Worker C  
**Depends on:** A3,A7

Build event-driven inbox projections for what changed, what needs the lawyer, what AI did, approvals, deadlines and exceptions. Inbox items are links to canonical Work/Matter state, not a second task database.

## Task C3 — Migration/import engine

**Owner:** Worker C  
**Depends on:** A3,A4

Implement discovery, mapping, import validation and provenance-preserving reconstruction for matter metadata, documents, templates, workflows, permissions and historical outcomes.

## Task C4 — Benchmark / Prove-It engine

**Owner:** Worker C  
**Depends on:** B6,C3

Run historical matters through configurable workflows and compare time, evidence coverage, errors, completion rate and human review. Results must be reproducible and data-scoped.

## Task C5 — Staffing and firm economics

**Owner:** Worker C  
**Depends on:** A3,A6,A7

Produce projections for lawyer/AI allocation, utilization, matter cost, write-offs, cycle time and recovered capacity. Calculations must cite source WorkItems/events and expose assumptions.

## Task C6 — Developer SDK and app manifest

**Owner:** Worker C  
**Depends on:** A0

Create app manifest schema, SDK types, lifecycle hooks, capability declaration format and local validation/test harness.

## Task C7 — App Registry and Marketplace

**Owner:** Worker C  
**Depends on:** C6,A2,A7

Implement developer submission, version registry, security metadata, review states, listing/search/read APIs and marketplace catalog. Published package versions are immutable.

## Task C8 — App capability/token service

**Owner:** Worker C  
**Depends on:** C6,A2

Issue narrowly scoped capability tokens and revoke them immediately. Support org/matter/resource/operation/expiry scope. Audit every grant/revoke.

## Task C9 — App runtime gateway/sandbox

**Owner:** Worker C  
**Depends on:** C8,B3

Route app calls through the gateway, enforce declared outbound destinations and capability checks, isolate app execution from database internals, and capture execution telemetry.

## Task C10 — Install/update/revoke lifecycle

**Owner:** Worker C  
**Depends on:** C7,C8,C9

Implement install, configure, enable, update, rollback, disable and uninstall. Uninstall revokes access and preserves canonical data.

## Task C11 — Extension points

**Owner:** Worker C  
**Depends on:** C9,B1,B5

Implement versioned extension contracts for command, matter_panel, workflow_node, agent, knowledge_provider, connector, artifact_renderer, event_handler and automation_rule.

---

## Task D1 — API/UI shell

**Owner:** Lead with A/C contributions  
**Depends on:** A3,B4,C2,C7

Build a thin but real application shell exposing Matter Workspace, Case Brain, Legal Inbox, Work execution, Evidence Report, Marketplace and Admin/Policy surfaces. No dead buttons; every visible action maps to a supported backend capability.

## Task D2 — End-to-end proof slices

**Owner:** Lead + all workers  
**Depends on:** A6,B8,C10,D1

Demonstrate complete flows:

1. Create org/user/matter → ingest source → EvidenceLink → Case Brain.
2. Create legal objective → compile Work Graph → execute AI Workforce → verify → approve artifact.
3. Install marketplace app → grant matter-scoped capability → invoke app extension → audit → revoke → confirm denial.
4. Import historical matter → benchmark against baseline → produce reproducible Prove-It report.
5. Record outcome → update Firm Brain → use resulting knowledge in a subsequent work plan without violating source permissions.

---

## Integration gates

A worker can merge independently only when its contract tests pass and its public interfaces match the frozen docs. The tech lead owns integration commits and must not accept undocumented cross-lane coupling.

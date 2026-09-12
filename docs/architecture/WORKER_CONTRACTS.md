# Spector — Worker Contracts

**Purpose:** allow a tech lead to dispatch up to three workers concurrently without shared-contract drift.

## Worker A — Core Work System

### Owns

- organization/tenant model;
- user/team/role/policy model;
- matter and matter ACLs;
- KnowledgeSource/SourceVersion metadata;
- documents and provenance metadata;
- Legal Work Graph persistence/projections;
- EvidenceLink persistence;
- canonical event envelope and event persistence;
- Case Brain/Firm Brain projections;
- authorization service;
- persistence/repository layer;
- core API contracts.

### Must not own

- agent planning/execution;
- model routing;
- marketplace runtime;
- UI-specific state;
- vendor-specific authority behavior.

### Produces contracts

- stable resource IDs;
- entity repositories/services;
- authorization decisions;
- event types from `DOMAIN_MODEL.md`;
- `EvidencePacket` shape;
- graph query interfaces;
- Case Brain/Firm Brain read models.

## Worker B — Execution System

### Owns

- Work Compiler;
- Workflow Definition/Version;
- Agent Contract;
- durable workflow orchestration;
- AgentRun and ToolInvocation execution;
- Model Gateway;
- Tool Registry;
- Evidence/verification engine;
- Artifact generation pipeline;
- Approval orchestration.

### Must not own

- tenant authorization policy definitions;
- direct SQL against Worker A tables;
- app installation/marketplace policy;
- frontend routing.

### Consumes

- authorization checks from A;
- Matter/Work/Evidence interfaces from A;
- external tool contracts;
- model-provider adapters behind the Model Gateway.

### Produces

- deterministic Work Graph execution;
- AgentRun lifecycle events;
- Evidence Reports;
- artifacts/approval requests.

## Worker C — Platform & Ecosystem

### Owns

- Legal Inbox/event subscriptions;
- authority/connectors framework;
- migration/import pipeline;
- historical benchmark/prove-it engine;
- AI staffing/economics projections;
- developer SDK;
- App Registry/Marketplace;
- app publication/review/install/update/uninstall;
- app runtime gateway;
- extension points;
- platform-level integrations.

### Must not own

- canonical matter/work persistence;
- direct database credentials for apps;
- internal AI execution bypasses;
- unauthorized EvidenceLink mutation.

### Produces

- connector interfaces;
- app manifest/schema;
- capability/token contracts;
- extension-point contracts;
- marketplace APIs;
- integration events.

## Shared contract rules

1. A shared interface is defined once in `docs/architecture` before worker implementation.
2. Workers consume interfaces, not each other's private modules.
3. Cross-lane changes require an issue update and architecture review before merge.
4. No worker may silently rename an event/resource/capability.
5. Adapters are allowed at boundaries; domain leakage is not.
6. Tests must include contract tests for every cross-worker interface.

## Integration checkpoints

### Checkpoint 0

A, B and C agree on canonical IDs, event envelope, auth semantics and package/versioning.

### Checkpoint 1

A exposes Matter/Work/Evidence APIs; B and C can integrate without touching A's storage.

### Checkpoint 2

B exposes execution APIs; C can invoke approved workflows/apps through public contracts.

### Checkpoint 3

C exposes app runtime/marketplace interfaces; B can invoke app-provided tools only through capability checks.

### Checkpoint 4

End-to-end vertical slices validate the architecture: create matter → ingest source → create work → execute agent/workflow → verify evidence → approve artifact → record outcome → install app → invoke app extension.

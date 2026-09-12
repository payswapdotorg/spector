# Spector — Canonical Domain Model

**Status:** FROZEN v1.0

## Aggregate roots

### Organization

Tenant boundary and security root. Holds users, teams, policies, subscriptions, installed apps, integrations and firm-level knowledge.

Required fields: `id`, `name`, `status`, `data_region`, `created_at`, `updated_at`.

### Matter

Primary legal-work context. Belongs to exactly one Organization. Contains parties, matter ACLs, sources, work items, workflows, artifacts and outcomes.

Required fields: `id`, `organization_id`, `client_id`, `status`, `practice_area`, `jurisdiction`, `open_date`, `close_date`, `conflict_state`.

### WorkItem

Canonical unit of execution and audit. May be matter-bound or organization-level.

Required fields: `id`, `organization_id`, `matter_id?`, `objective`, `status`, `priority`, `created_by`, `assigned_to?`, `workflow_definition_version?`, `budget`, `created_at`.

### KnowledgeSource / SourceVersion

A logical source and its immutable versions. A SourceVersion has content hash, provenance, ACL snapshot, effective dates and ingestion status.

### EvidenceLink

First-class provenance object connecting a claim/output span to an immutable SourceVersion locator.

Required: `claim_id`, `source_version_id`, `locator`, `quote_hash?`, `authority_rank`, `created_at`.

### Workflow

Versioned typed work graph. Published workflow versions are immutable. A workflow can be invoked by users, system rules or apps if policy allows.

### AgentRun

Execution instance of an Agent/Task node. Contains data scope, model route, tool calls, budget usage, checkpoints, outputs, evaluator results and failure/escalation state.

### Artifact

A user-meaningful produced object: memo, draft, redline, chronology, research report, spreadsheet, task list, communication, etc. Artifacts are versioned and linked to Evidence/Work Items.

### Approval

Explicit human authorization required before a configured consequential action becomes effective.

### Outcome

Post-work observation attached to a WorkItem/Matter, containing outcome type, confidence, source, reviewer and optional financial/time metrics. Outcome data is used for learning only after tenant policy permits it.

## Knowledge entities

```text
KnowledgeSource 1─* SourceVersion
SourceVersion 1─* Document
Document 1─* DocumentVersion
DocumentVersion 1─* Section/Span
Section/Span ──* Claim
Claim ──* EvidenceLink ──1 SourceVersion
Authority ──* CitationEdge ──* Authority
```

## Work graph entities

```text
Organization ──* Matter
Matter ──* WorkItem
WorkItem ──* AgentRun
AgentRun ──* ToolInvocation
AgentRun ──* Artifact
WorkItem ──* Approval
WorkItem ──* Outcome
WorkItem ──* EvidenceLink
```

## People and access

```text
Organization ──* User
User ──* TeamMembership
Role/Policy ──* Grant
Matter ──* MatterAccess
Matter ──* EthicalWallRule
AppInstallation ──* GrantedCapability
```

No authorization is inferred merely because an object is retrievable. Access checks are explicit and contextual.

## App ecosystem entities

```text
Developer ──* App
App ──* AppVersion
AppVersion ──* CapabilityDeclaration
Organization ──* AppInstallation
AppInstallation ──* GrantedCapability
AppVersion ──* AppReview
AppVersion ──* SecurityScan
AppExecution ──* AuditEvent
```

## Identifiers and versioning

- IDs are opaque UUID/ULID-style identifiers; never expose sequential database IDs as public identifiers.
- Public API resources use stable resource IDs and explicit version identifiers.
- Immutable resources use content hashes for integrity checks.
- Workflow, app and connector versions are semver-like and immutable once published.
- Every event carries `event_id`, `organization_id`, `occurred_at`, `actor`, `correlation_id`, and `causation_id`.

## Canonical events

Initial event vocabulary:

- `matter.created`
- `matter.updated`
- `work.created`
- `work.started`
- `work.completed`
- `work.failed`
- `evidence.linked`
- `artifact.created`
- `approval.requested`
- `approval.granted`
- `approval.rejected`
- `outcome.recorded`
- `knowledge.ingested`
- `workflow.published`
- `workflow.invoked`
- `agent.run.started`
- `agent.run.completed`
- `app.submitted`
- `app.approved`
- `app.rejected`
- `app.installed`
- `app.uninstalled`
- `app.capability.granted`
- `app.capability.revoked`
- `app.execution.started`
- `app.execution.completed`

Event names are part of the public internal contract. Do not rename them casually; use compatibility aliases when migration is required.

## Data ownership

Canonical truth lives in Spector for:

- organizations and security policy;
- matters and work items;
- source/version provenance metadata;
- evidence links;
- workflow definitions and run history;
- artifacts and approvals;
- app registry/installations/capabilities;
- audit events.

External systems remain systems of record for their own domains. Connectors create provenance-preserving projections; they do not silently copy ownership semantics.

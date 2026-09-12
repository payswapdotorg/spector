# Spector — App Marketplace Architecture

**Status:** FROZEN v1.0

## 1. Purpose

The App Marketplace makes Spector an extensible legal-work ecosystem. Spector itself owns the legal-work kernel; developers extend capabilities through governed apps without gaining unrestricted access to Spector internals.

Apps may be published by Spector, law firms, legal technology companies, individual developers, or approved partners.

## 2. App categories

An app can extend one or more capability families:

- **Knowledge:** legal authorities, specialist databases, firm knowledge packs.
- **Connectors:** DMS, email, CRM, billing, practice management, Word/Office, research systems.
- **Workflow:** due diligence, litigation chronology, contract review, regulatory checks, intake, closing.
- **Agents:** bounded specialist agents that implement an Agent Contract.
- **Artifacts:** specialized document/report/spreadsheet generation.
- **Operations:** staffing, pricing, billing, client communication, matter management.
- **UI/Workspace:** panes, cards, commands or matter tabs using approved extension surfaces.
- **Developer tools:** SDKs, test harnesses, evaluation packs and administrative tools.

Apps do not create a second Spector kernel.

## 3. App package

Every published version is a signed/hashed immutable package with:

- app ID and developer ID;
- semantic version;
- manifest version;
- human-readable name/description/icon;
- declared extension points;
- requested capabilities;
- data classification requirements;
- supported Spector API versions;
- runtime requirements;
- webhook/event subscriptions;
- outbound domains;
- model/provider requirements, if any;
- pricing/entitlement metadata;
- support/security contact;
- permissions rationale;
- release notes;
- integrity hash;
- publisher signature/attestation;
- security scan result;
- review status.

## 4. Capability security model

Apps receive **capability tokens**, not database credentials.

A capability is scoped by:

`organization + app installation + resource class + operation + optional matter + expiry/policy`.

Example:

```json
{
  "capability": "document.read",
  "organizationId": "org_123",
  "matterScope": ["matter_abc"],
  "operations": ["read"],
  "expiresAt": "2026-10-01T00:00:00Z"
}
```

Capabilities are denied by default, minimized to the requested surface, and revocable immediately.

## 5. Extension model

Apps may participate through typed extension points:

- `command` — user-invoked command/action.
- `matter_panel` — matter workspace UI contribution.
- `workflow_node` — executable typed workflow step.
- `agent` — bounded specialist agent.
- `knowledge_provider` — retrieval/evidence provider.
- `connector` — bidirectional external system integration.
- `artifact_renderer` — typed output renderer.
- `event_handler` — event-triggered work.
- `automation_rule` — policy-governed trigger/action.

An extension point has a versioned schema and explicit lifecycle. Unknown fields must be safely ignored for forward compatibility.

## 6. Runtime isolation

Default trust level is **sandboxed remote app**. The app communicates through Spector's API gateway and event bus.

No marketplace app can:

- connect directly to PostgreSQL;
- read raw object-storage buckets outside granted resources;
- impersonate a user;
- bypass Matter ACLs;
- write EvidenceLinks directly without provenance validation;
- mutate canonical audit events;
- invoke unrestricted tools;
- silently exfiltrate customer data to undeclared destinations.

A future higher-trust first-party/native tier may exist, but it is a separate security tier and cannot weaken the sandbox contract.

## 7. App lifecycle

```text
Draft → Validate → Security Scan → Publish Request → Review
     → Approved → Marketplace Listed → Install → Configure
     → Enabled → Version Update / Rollback → Disable / Uninstall
```

Uninstall revokes capabilities and stops new execution. Canonical Spector data remains intact unless a separately authorized deletion operation applies.

## 8. Publication and trust

Publication requires automated checks:

- manifest/schema validation;
- dependency validation;
- package integrity/signature verification;
- malware/dependency scanning;
- permission risk analysis;
- declared-domain/network policy checks;
- API compatibility checks;
- privacy/data-handling declaration;
- test-suite/evaluation evidence for AI apps.

Sensitive/high-risk apps require human review.

## 9. Marketplace discovery

Search and ranking may use:

- category;
- practice area;
- jurisdiction;
- compatibility;
- verified publisher;
- security/trust status;
- ratings/reviews;
- installation count;
- recency;
- supported extension points;
- measured reliability/latency.

Ranking must not be allowed to override security/policy eligibility.

## 10. AI app requirements

Agent/workflow apps must declare:

- objective and boundaries;
- allowed tools;
- allowed data classes;
- input/output schemas;
- max iterations;
- budget ceilings;
- model policy;
- evidence expectations;
- evaluator;
- escalation conditions;
- human approval requirements.

An AI app that produces legal claims must emit structured evidence references or an explicit unsupported-claim status.

## 11. Billing / monetization

Marketplace pricing may support free, one-time, subscription, usage-based, organization-seat, or hybrid models.

Spector's marketplace billing is an entitlement layer, not the app's source of truth for legal/billing records. Entitlement changes emit events and are enforced by capability grants.

## 12. Version compatibility

The SDK and app manifest declare supported API versions. Breaking platform changes require a new major API version. Apps remain pinned to the installed app version until an approved update is applied.

## 13. Governance

Every app installation is auditable:

`who installed → why/consent → capabilities granted → configuration → executions → data accessed → outputs → errors → revocation/uninstall`.

Tenant admins can inspect and revoke apps. Matter-level policies may further restrict an installed app.

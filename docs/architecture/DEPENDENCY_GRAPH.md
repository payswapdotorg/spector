# Spector — Dependency Graph

The graph is deliberately optimized for **three concurrent workers** and small integration surfaces.

## Nodes

| ID | Work item | Owner | Depends on |
|---|---|---|---|
| A0 | Repository/tooling bootstrap | Lead | — |
| A1 | Canonical schema + migrations | A | A0 |
| A2 | Identity/RBAC/ABAC + matter ACL | A | A1 |
| A3 | Matter + WorkItem APIs | A | A1,A2 |
| A4 | Knowledge/source/evidence model | A | A1,A2 |
| A5 | Legal Work Graph projection/query layer | A | A3,A4 |
| A6 | Case Brain/Firm Brain read models | A | A5 |
| A7 | Event/audit infrastructure | A | A1,A2 |
| B1 | Agent/Workflow contracts | B | A0 |
| B2 | Model Gateway + provider adapters | B | B1 |
| B3 | Tool Registry + policy bridge | B | B1,A2 |
| B4 | Work Compiler | B | B1,A3,A5 |
| B5 | Durable workflow runtime | B | B1,B3 |
| B6 | Evidence/verification engine | B | A4,A5,B1 |
| B7 | Artifact/approval execution | B | B5,B6 |
| B8 | AI Workforce catalog/execution | B | B2,B4,B5,B6 |
| C1 | Connector/authority framework | C | A4,A2 |
| C2 | Legal Inbox/event consumers | C | A7,A3 |
| C3 | Migration/import engine | C | A3,A4 |
| C4 | Benchmark/prove-it engine | C | B6,C3 |
| C5 | Staffing/economics projections | C | A3,A6,A7 |
| C6 | Developer SDK + app manifest | C | A0 |
| C7 | App Registry/Marketplace | C | C6,A2,A7 |
| C8 | App capability/token service | C | C6,A2 |
| C9 | App runtime gateway/sandbox | C | C8,B3 |
| C10 | App install/update/revoke lifecycle | C | C7,C8,C9 |
| C11 | Extension points (workflow/agent/UI/event) | C | C9,B1,B5 |
| D1 | API/UI shell | Lead + A/C | A3,B4,C2,C7 |
| D2 | End-to-end vertical slices | Lead + all | A6,B8,C10,D1 |

## Parallelization waves

### Wave 0 — Foundation

`A0` only.

### Wave 1 — maximum concurrency

```text
Worker A: A1 → A2 → A3/A4/A7
Worker B: B1 → B2
Worker C: C1 + C6
```

C1 can begin from frozen contracts and does not need to wait for implementation of A4; use adapter mocks only at the contract boundary, never fake canonical persistence.

### Wave 2

```text
Worker A: A5 → A6
Worker B: B3 → B4 → B5
Worker C: C2 → C3 → C8
```

### Wave 3

```text
Worker A: hardening/contract tests
Worker B: B6 → B7 → B8
Worker C: C4 + C5 + C7
```

### Wave 4

```text
Worker C: C9 → C10 → C11
Worker Lead/A: D1
```

### Wave 5

`D2` only, with all three workers involved in integration verification.

## Minimal dependency rules

- Never make two independent work items depend on each other's implementation. Depend on a contract.
- Prefer interface packages/schema files over source imports across lanes.
- A worker may use a test double only where the contract is already frozen and the double is replaced at integration; doubles must never satisfy end-to-end completion.
- Shared schema changes land before consumers.
- Each issue contains its exact dependencies and explicit "do not modify" boundaries.

## Critical path

`A0 → A1 → A2 → A3 → A5 → B4 → B5 → B8 → C11 → D2`

Marketplace critical path:

`A0 → A2 + C6 → C8 → C9 → C10 → C11 → D2`.

Knowledge/evidence critical path:

`A0 → A1/A2 → A4 → A5 → B6 → B8 → D2`.

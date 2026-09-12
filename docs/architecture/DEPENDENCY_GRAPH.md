# Spector — Dependency Graph

The graph is deliberately optimized for **three concurrent workers** and small integration surfaces.

## Nodes

| ID | Issue | Work item | Owner | Depends on |
|---|---:|---|---|---|
| A0 | #1 | Repository/tooling bootstrap | Lead | — |
| A1 | #2 | Canonical schema + migrations | A | A0 |
| A2 | #3 | Identity/RBAC/ABAC + matter ACL | A | A1 |
| A3 | #4 | Matter + WorkItem APIs | A | A1,A2 |
| A4 | #5 | Knowledge/source/evidence model | A | A1,A2 |
| A5 | #6 | Legal Work Graph projection/query layer | A | A3,A4 |
| A6 | #7 | Case Brain/Firm Brain read models | A | A5 |
| A7 | #8 | Event/audit infrastructure | A | A1,A2 |
| B1 | #9 | Agent/Workflow contracts | B | A0 |
| B2 | #10 | Model Gateway + provider adapters | B | B1 |
| B3 | #11 | Tool Registry + policy bridge | B | B1,A2 |
| B4 | #12 | Work Compiler | B | B1,A3,A5 |
| B5 | #13 | Durable workflow runtime | B | B1,B3 |
| B6 | #14 | Evidence/verification engine | B | A4,A5,B1 |
| B7 | #15 | Artifact/approval execution | B | B5,B6 |
| B8 | #16 | AI Workforce catalog/execution | B | B2,B4,B5,B6 |
| C1 | #17 | Connector/authority framework | C | A4,A2 |
| C2 | #18 | Legal Inbox/event consumers | C | A7,A3 |
| C3 | #19 | Migration/import engine | C | A3,A4 |
| C4 | #20 | Benchmark/Prove-It engine | C | B6,C3 |
| C5 | #21 | Staffing/economics projections | C | A3,A6,A7 |
| C6 | #22 | Developer SDK + app manifest | C | A0 |
| C7 | #23 | App Registry/Marketplace | C | C6,A2,A7 |
| C8 | #24 | App capability/token service | C | C6,A2 |
| C9 | #25 | App runtime gateway/sandbox | C | C8,B3 |
| C10 | #26 | App install/update/revoke lifecycle | C | C7,C8,C9 |
| C11 | #27 | Typed extension points | C | C9,B1,B5 |
| C12 | #29 | Workflow discovery + outcome learning | C | A6,A7,B8 |
| C13 | #30 | Matter Twin + scenario simulation | C | A6,B4,B6,B8 |
| D1 | #28 | API/UI shell | Lead + A/C | A3,B4,C2,C7 |
| D2 | #31 | End-to-end vertical proof slices | Lead + all | A6,B8,C10,D1,C12,C13 |

## Parallelization waves

### Wave 0 — Foundation

`A0` only.

### Wave 1 — maximum concurrency

```text
Worker A: A1
Worker B: B1
Worker C: C6
```

After A1 exists, Worker A continues A2. B can continue B2 without waiting on A2. C can build SDK validation independently because the app manifest/capability contracts are frozen.

### Wave 2

```text
Worker A: A2 → A3 + A4 + A7
Worker B: B2 → B3
Worker C: C6 → C8 + connector contract work
```

### Wave 3

```text
Worker A: A5 → A6
Worker B: B4 + B5 → B6
Worker C: C1 + C2 + C3 + C7
```

### Wave 4

```text
Worker A: contract/security hardening
Worker B: B7 → B8
Worker C: C4 + C5 + C9
```

### Wave 5

```text
Worker C: C10 → C11 → C12/C13
Lead/A/C: D1 when its direct contracts are ready
```

### Wave 6

`D2` only, with all workers involved in integration verification.

## Minimal dependency rules

- Never make independent work items depend on each other's private implementation. Depend on a versioned contract.
- Prefer shared schema/interface packages over cross-lane source imports.
- A test double may stand in only behind an already-frozen contract; it never satisfies end-to-end completion.
- Shared schema changes land before consumers.
- Each issue owns one independently testable deliverable and states what it must not modify.

## Critical paths

Core legal work:

`A0 → A1 → A2 → A3 → A5 → A6 → B4 → B5/B6 → B8 → D2`

Marketplace:

`A0 → C6 + A2 → C8/C7 → C9 → C10 → C11 → D2`

Learning/Twin:

`A6 → B8 + B4/B6 → C12/C13 → D2`

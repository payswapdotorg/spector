# Spector — Architecture Change Policy

The architecture is frozen to reduce LLM/worker drift. Changes are permitted, but only through explicit evidence-driven change control.

## Allowed without architecture review

- implementation details that do not alter a public contract;
- performance optimizations that preserve semantics;
- test additions;
- observability improvements;
- bug fixes restoring the frozen behavior.

## Requires architecture review

- adding/removing a domain object;
- changing an event name or semantics;
- changing authorization order or scope semantics;
- changing canonical data ownership;
- adding a cross-worker dependency;
- changing the Work Compiler output contract;
- changing Agent/Tool/Workflow contracts;
- changing EvidenceLink semantics;
- adding a marketplace capability or extension point;
- introducing a new storage system as a system of record;
- allowing apps to bypass the gateway;
- changing model-provider abstraction;
- making a formerly synchronous operation durable or vice versa.

## Required change record

An architecture change issue must state:

1. current invariant;
2. proposed change;
3. reason/evidence;
4. affected contracts;
5. migration/compatibility plan;
6. security implications;
7. tests proving old/new behavior as appropriate;
8. updated dependency graph.

## Drift prevention

Workers must first read the frozen docs. Generated implementation prompts must reference exact canonical files. A worker may not infer architecture from neighboring code when the docs define a contract.

When documentation and code disagree, stop and reconcile the discrepancy before extending the implementation. Never silently rewrite the frozen contract to match an implementation.

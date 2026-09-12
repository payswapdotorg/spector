# Spector — Tech Lead Takeover Handoff

## Role

You are the implementation tech lead. You may dispatch **at most three workers concurrently**. Your job is to preserve the frozen architecture while converting GitHub Issues into production implementation.

## First actions

1. Read `README.md`.
2. Read all files in `docs/architecture/`.
3. Read `docs/governance/DEFINITION_OF_DONE.md` and `ARCHITECTURE_CHANGE_POLICY.md`.
4. Read `docs/implementation/IMPLEMENTATION_PLAN.md` and `DEPENDENCY_GRAPH.md`.
5. Inspect the repository and CI before assigning implementation work.
6. Assign only issues whose dependencies are satisfied.

## Dispatch strategy

Use three workers as lanes:

- **A / Core Work System:** persistence, authorization, matter/work, knowledge/evidence, graph, brains, audit/events.
- **B / Execution System:** execution contracts, model gateway, tools, compiler, durable runtime, verification, artifacts, AI workforce.
- **C / Platform & Ecosystem:** connectors, inbox, migration, benchmark, economics, SDK, marketplace, capabilities, runtime, extension points.

Keep each worker's changes inside its lane unless an issue explicitly calls for a shared contract. Cross-lane changes must land through the frozen contract first.

## Definition of a good issue

An issue must have:

- one independently testable deliverable;
- a named owner lane;
- exact dependencies;
- exact files/modules to create or modify after the implementation stack exists;
- public interfaces it consumes/produces;
- negative/security acceptance criteria;
- a clear list of things it must not change.

Avoid issues such as "build the AI system" or "finish backend". Split work until a reviewer can accept/reject one change without judging unrelated work.

## Merge discipline

Workers should commit frequently. The tech lead reviews each worker result for:

- contract compliance;
- test evidence;
- security boundary preservation;
- observability;
- absence of placeholder/mock behavior;
- no accidental dependency on another worker's private code.

Do not merge a worker because the UI looks correct if the underlying contract is unverified.

## Required first vertical slice

The first credible integrated slice is:

`organization/user → matter → source/version → evidence → work item → compiled plan → bounded agent run → verification → artifact → approval → outcome`.

The first ecosystem slice is:

`developer → app manifest → validation/security → publish → install → scoped capability → extension invocation → audit → revoke/uninstall`.

## Marketplace implementation rule

Treat the marketplace as a core platform primitive from the beginning. Do not bolt it onto a finished monolith. The public extension contract, capability model, app versioning and runtime gateway must remain separate from the canonical legal-work kernel.

## Completion bar

The system is not considered architecturally implemented until all D2 proof flows pass and the marketplace example app demonstrates real capability enforcement, not a simulated installation.

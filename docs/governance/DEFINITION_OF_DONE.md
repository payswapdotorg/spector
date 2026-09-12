# Spector — Definition of Done

A work item is **not complete** because code exists or unit tests pass. It is complete only when its behavioral contract, security properties, observability and integration evidence are demonstrated.

## Required for every issue

1. Code follows the frozen architecture and worker boundary.
2. No undocumented schema/event/API contract was introduced.
3. Unit tests cover core behavior and negative paths.
4. Contract tests cover every cross-worker interface.
5. Authorization is tested for allowed and denied cases.
6. Errors are typed and observable; silent fallback is prohibited.
7. Idempotency is proven for retried operations where side effects exist.
8. Audit events identify actor, organization, correlation and causation.
9. Data provenance is preserved for legal outputs.
10. Documentation is updated when public behavior changes.

## Legal correctness gates

- Consequential claims have EvidenceLinks or are explicitly marked unsupported.
- Evidence points to immutable source versions and precise locators.
- Authority validity is evaluated for jurisdiction/date where applicable.
- Contradictory evidence is surfaced rather than silently averaged away.
- Human approval is enforced for policy-defined consequential actions.

## Marketplace gates

A marketplace issue is done only when:

- manifest validation passes;
- declared capabilities are enforced;
- package integrity is verified;
- security scan metadata is persisted;
- app execution is isolated from canonical storage;
- install/update/rollback/uninstall works;
- capability revocation immediately blocks access;
- all app executions are auditable;
- app-produced legal claims follow the EvidenceLink contract;
- an example app passes the complete lifecycle.

## Integration gate

Before marking the architecture implementation complete, the tech lead must run the D2 vertical proof flows from `docs/implementation/IMPLEMENTATION_PLAN.md` and preserve test/run evidence in the pull request.

## Anti-completion signals

The following do **not** qualify as done:

- mock-only backend implementations;
- UI screens without real API behavior;
- placeholder agent outputs presented as production AI;
- fake marketplace installations that do not enforce capabilities;
- tests that assert implementation details but not security behavior;
- benchmark reports generated from synthetic results while the issue claims historical validation;
- a connector that returns data without provenance;
- a workflow that cannot resume after process failure;
- an app that can access data by knowing an object ID alone.

# Spector — Legal Work Operating System

Spector is the implementation repository for the Legal Work Operating System architecture approved for this project.

## Repository status

This repository was initialized as a clean-room implementation repository on 2026-09-12. The GitHub repository was empty at bootstrap, so there is no legacy application architecture to preserve.

## Canonical implementation sources

Read these in order before implementing any code:

1. `docs/architecture/FROZEN_ARCHITECTURE.md` — canonical system architecture and non-negotiable invariants.
2. `docs/architecture/DOMAIN_MODEL.md` — canonical domain objects and relationships.
3. `docs/architecture/APP_MARKETPLACE.md` — canonical extension and marketplace architecture.
4. `docs/architecture/WORKER_CONTRACTS.md` — boundaries and handoff contracts for the three concurrent worker lanes.
5. `docs/architecture/DEPENDENCY_GRAPH.md` — dependency DAG and parallelization rules.
6. `docs/implementation/IMPLEMENTATION_PLAN.md` — implementation waves and task sequencing.
7. `docs/governance/DEFINITION_OF_DONE.md` — completion and evidence gates.
8. `docs/governance/ARCHITECTURE_CHANGE_POLICY.md` — rules for preventing architectural drift.

GitHub Issues are the executable work items. Each issue is intentionally scoped to minimize cross-worker dependency.

## Core product thesis

Spector is not a legal chatbot. It is the operating system for legal work: a permissioned, matter-centric system that models legal work as a graph, executes bounded AI work through durable workflows, preserves evidence and provenance, learns firm-specific workflows and outcomes, and exposes the resulting capabilities through a governed application ecosystem.

## Worker model

A tech lead may dispatch up to three implementation workers concurrently:

- **Worker A — Core Work System:** tenant/matter/work graph, knowledge/evidence, authorization, persistence, eventing, Case Brain/Firm Brain.
- **Worker B — Execution System:** Work Compiler, AI Workforce, workflow runtime, model gateway, verification, artifacts/approvals.
- **Worker C — Platform & Ecosystem:** integrations, legal inbox, staffing/economics, migration/benchmarking, developer SDK, app registry/marketplace/install/runtime sandbox.

Workers must not invent shared contracts. Shared schemas, event names, IDs, policies, and extension interfaces come from the frozen architecture docs.

## Required principle

**Authority is a capability, not the product boundary.** Spector must be able to use licensed or public legal authorities through connectors while remaining the system of record for work, provenance, workflows, decisions, memory, and firm operations.

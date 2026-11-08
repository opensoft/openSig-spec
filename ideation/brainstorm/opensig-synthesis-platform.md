# Synthesis: Shared Platform and Domain Authority — Brainstorm

Status: brainstorm
Kind: architecture
Summary: One Apache-2.0 platform supports independent consumer adoption while preserving clinical, financial and human authority.
Topics: opensig, shared-platform, synthesis
Repository context: openSig; cross-application delivery
Captured: 2026-11-08

## Possible feats

- **Independent adoption** — reuse signing without coupling all consumer release schedules.

## Members and their joints

Members: [licensing](opensig-licensing.md), [workflow/adapters](opensig-workflow-adapters.md), [implementation plan](opensig-implementation-plan.md).

### Contract and domain ownership

openSig owns envelope/evidence state. Each consumer binds source record/version and tenant, verifies callbacks and rechecks current domain authority before applying results. Message authenticity does not authorize arbitrary clinical or financial actions. Hermes prepares and requests within scope; openChart owns clinical attestation/amendments; openPractice owns administrative/financial transitions.

### Packaging and delivery

The Apache-2.0 decision and shipped MIT/Apache allowlist gate framework and engine choices. Framework-neutral contracts isolate consumers from core runtime choices. Shared SDK/schema compatibility tests permit different release schedules. openPractice's missing runtime blocks its own acceptance, not core/Hermes delivery.

### Operational trust

Dedicated signing keys, wallet keys and identities remain separate from AI credentials. Discover the live target before rollout. Prove source/CI, deployed artifact and real signing separately. Corporate pilot feedback informs healthcare readiness without expanding agent authority.

## Emergent behavior and open questions

Shared templates/evidence reduce duplicated code while each domain stays independently usable. Resolve operational owner, framework and identity early; retain domain-specific consent/retention rules in consumer governance. Future witnesses/offline flows extend explicit contracts rather than bypass human approval.

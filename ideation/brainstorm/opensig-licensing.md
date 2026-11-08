# Licensing and Platform Boundaries — Brainstorm

Status: brainstorm
Kind: architecture
Summary: All original openSig components use Apache-2.0 with an MIT/Apache allowlist for third-party shipped materials.
Topics: opensig, licensing, component-boundaries
Repository context: openSig; Hermes, openChart and openPractice integrations
Captured: 2026-11-08

## Possible feats

- **License gate** — inventory and validate every shipped dependency and asset.

## Agreed decisions

The names are openSig, openAutograph and openCryptograph. All original modules, adapters, SDKs and documentation will use Apache-2.0. Third-party MIT/Apache components retain their own licenses and notices. Do not copy GPL/AGPL/LGPL signing implementations or assume an existing similarly named project is the base.

The allowlist covers direct and transitive packages, frontend bundles, fonts, assets and distributed container contents. Generate an SBOM, pin artifacts, retain NOTICE/attribution requirements and reject unknown/disallowed licenses. An Apache repository header does not license an entire runtime environment. Public name, trademark and package availability remain unchecked.

## Proposed packaging

One original Triad project: openSig assembly root, openSig-spec specifications and openSig-code implementation. The code leg contains separate workflow/API, openAutograph UI, openCryptograph worker/verifier, consumer adapters and optional anchoring adapters. Prefer a standalone service with framework-neutral HTTP contracts. Frappe-native adapters fit openChart/openPractice; choosing Frappe for the core depends on runtime and dependency qualification. Do not silently relax the license rule to accommodate a framework.

## Open questions

Resolve repository owner/publication, core framework, build packaging and supported deployment matrix during phase 0. This scaffold is local documentation, not a published repository or deployed application.

## Relationships

[Platform synthesis](opensig-synthesis-platform.md); [implementation plan](opensig-implementation-plan.md).

# Implementation Plan for Signing Authority

**Branch** `002-signing-authority`

**Date** 2026-11-23

**Reviewed input** [Feature 002 specification](spec.md) at `73247258a6a31acb323cbe04e146b38450c193a6`

## Summary

Start with a corporate records intake and review workflow. Deliver one OpenSig
web UI for direct upload and restricted SharePoint selection, then host it as
a Teams personal tab. Both sources enter the same private review pipeline.
Record the exact accepted bytes and source version before review. Keep
autograph-only signing available. Full certification later consumes reviewed
evidence and verifies the human and authority at each signing action.

## Technical Context

| Area | Baseline and decision |
| --- | --- |
| Runtime | Reuse openSig-code's pinned Node.js 24.22.2 and TypeScript stack, same server and browser conventions. |
| Intake | Use existing OpenSig operator authorization and tenant boundaries. Add a Teams personal tab and a server-side Microsoft Graph v1.0 adapter for a dedicated read-only SharePoint library. Start with PDF and DOCX governance evidence. |
| Storage | Reuse tenant SQLite transactions, private content-addressed storage and audit writer for immutable evidence references. Keep governance evidence outside the signing PDF's own upload path and public package. |
| Authentication | Teams sign-in establishes the intake operator's identity. The server checks OpenSig tenant, entity and role before delegated Graph access. Recipient identity proof and signing authorization remain in the signing ceremony. |
| Testing | Use the existing verification runner and isolated synthetic browser harness. Run fake Graph and source-race tests on the normal runner, then qualify the real connector in an approved test tenant. |
| Pilot boundary | One approved tenant and dedicated document library. Start with web and Teams desktop/web. Limit the pilot to explicit imports, with no background sync, cross-tenant channel access, production credentials or live signing. |
| Operational bounds | Browse one bounded page at a time. Stream bounded file reads and preserve retry safety. Specify deployment limits and verify them with fixtures before accepting the intake slice. |

The code integration base must contain the reviewed Feature 001 runtime and
the intended migration and runner baseline. The current published code at
`97a687eace0a766ff6cc33215a2f54d2b4f52903` is a planning reference. Reconcile the
actual base, migration allocation and runner additions before implementation
so Feature 002 does not depend on another feature's working changes.

## Constitution Check

| Principle | Planning and design result |
| --- | --- |
| Apache-2.0 and shipped dependency allowlist | Reuse the current stack. Qualify the exact Teams browser SDK or Entra library and all shipped transitive assets before adding them. Record notices and SBOM evidence. |
| Personal approval stays with the human | Teams authentication, intake review, operator confirmation and human signing have separate permissions and evidence. Services cannot perform recipient identity proof or approval. |
| Verify before completion | Certification must pass the authority and signature checks for the same revision before publication. Certificate integrity, certificate trust and signing authority have distinct results. |
| Synthetic and private by default | Use synthetic local fixtures and an approved test library. Keep source bytes, identity evidence, transient download URLs and credentials out of public verification and logs. |
| Spec and code ownership | Keep the plan and contracts in openSig-spec. Implement in openSig-code. Independent acceptance precedes release and future assembly pin changes. |

Planning gates pass through the Phase 1 design. Tenant grants, live endpoint
behavior and app installation are release gates recorded in [Quickstart](quickstart.md).
No constitution exception is requested.

## Project Structure

### Documentation

- [Technical decisions and evidence](research.md)
- [Data model and state rules](data-model.md)
- [Intake interfaces](contracts/intake.md)
- [Signing and verification interfaces](contracts/certification.md)
- [Synthetic and controlled tenant acceptance](quickstart.md)

### Code placement in openSig-code

| Existing area | Planned change |
| --- | --- |
| `src/server/`, `src/api/auth/` | Serve the shared intake UI and verify operator or Teams sessions, with independent tenant and entity checks. |
| `src/api/`, `src/domain/` | Add a corporate evidence intake and review boundary, then bind a reviewed authority decision to a certified signing request. |
| `src/integrations/` | Add the Graph adapter and Teams host integration. Keep provider-specific behavior out of evidence review and finalization. |
| `src/storage/` | Add tenant-scoped evidence records, private blob references and audit coverage. Include backup, restore, retention and orphan recovery. |
| `src/api/recipients/`, `src/domain/finalization/`, `src/artifacts/` | Integrate human signing-time gates and export the protected certification summary. |
| `tests/`, `scripts/verify-modes.mjs` | Add Feature 002 synthetic service and browser checks. Preserve the Feature 001 modes and use the governed amendment process for runner registration. |

## Delivery Sequence

1. **Intake and review with direct upload.** Build the shared operator UI and
   private evidence pipeline. Prove provenance review, status, safe retries,
   replacement versions, and isolation using synthetic Viktory records.
   First gate is the intake portion of VIK-02 and VIK-06.
2. **Teams and SharePoint intake.** Open the same UI in Teams, add a custom
   selector rooted at the approved library, and use delegated read access.
   Prove exact-version import, access denial and source immutability locally.
   Use the controlled tenant to qualify real Teams SSO and Graph behavior.
   First gate is VIK-03 plus the intake checks in Quickstart.
3. **Authority and certified signing.** Add an entity and authority decision
   gate, signing-time identity and authority checks, and mode-aware completion.
   Verify both signing modes and package privacy through the full VIK-01 to
   VIK-12 matrix. Run the normal regression and independent acceptance on the
   exact published implementation candidate.

The intake slice produces reviewed evidence for a signer authority decision.
The signing slice is required before the first complete VIK-02 run or any
blue-check certification is accepted.

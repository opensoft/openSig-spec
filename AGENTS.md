This is the **spec leg** of openSig (`opensig`): requirements, decisions
and acceptance criteria.

**The project's rules are not here.** They are in the assembly root
`opensoft/openSig`, in `AGENTS-shape.md` — read that before touching anything
that spans the legs. This leg is mounted there at `spec/`, and the other leg,
`opensoft/openSig-code`, beside it at `../code/`, once the root is cloned with
`--recurse-submodules`.

Working here is ordinary — an ordinary repository on an ordinary branch. What
advancing this leg does NOT do is advance the project: that is ONE commit in
`opensoft/openSig` moving the gitlink, `contracts/spec-pin.yaml` and every
workflow `@<sha>` for this leg together.

Being the spec leg confers no authority over specifications. The split is
navigation.

## openSig project rules

All original project work is Apache-2.0. Third-party shipped code, assets and dependencies must pass an MIT/Apache license allowlist. Never commit real patient data, signature images, credentials, private keys or wallet secrets. Use synthetic fixtures.

Keep clinical attestation and amendments in openChart, financial authority in openPractice, and human personal approval outside Hermes service credentials. Optional anchoring publishes commitments only and does not block ordinary signing.

Use the shared user-level OpenSpec/Speckit protocol. Canonical governance and feature plans live in the spec leg; implementation and tests live in the code leg. Shape rules live in the assembly root's AGENTS-shape.md. No signing runtime/test commands exist yet; establish them with the first governed implementation.

<!-- SPECKIT START -->
Current planning feature: [001-corporate-signing-mvp](specs/001-corporate-signing-mvp/plan.md). The canonical build checklist is in that feature's tasks.md; no runtime is implemented by this planning change.
<!-- SPECKIT END -->

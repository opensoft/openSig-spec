# openSig Triad Placement

The owner elected the Triad on 2026-11-08 and confirmed public Opensoft repositories, including explicit authorization of the upstream-organization and unattended scaffold flags.

| Repository | Mount | Responsibility |
|---|---|---|
| opensoft/openSig | . | Assembly manifest, exact leg pins, shape tooling and bootstrap |
| opensoft/openSig-spec | spec/ | Ideation, canonical OpenSpec changes/specifications and Speckit feature plans |
| opensoft/openSig-code | code/ | openSig workflow/API, openAutograph, openCryptograph, consumer adapters and optional anchoring |

All original project materials are Apache-2.0. Third-party shipped materials must pass the MIT/Apache allowlist. Component names do not replace the spec/code repository roles.

The scaffold was generated from clean openRepoShape commit 24769920611b214ea7426c51a220b324602c0861. Initial bootstrap passed naming, manifest, both exact leg pins/tree digests and all eleven copied shape-file digests. The source documentation was preserved separately before import.

Full proposals, canonical openspec/ and Speckit specs/ live in this spec leg. Feature worktrees use worktrees/<NNN-feature>/{spec,code}/ under the assembly root. Bounded amendments follow the shared Triad workflow. Advancing a leg requires one assembly commit updating its gitlink, pin manifest and any workflow reference together. Shape confers no authority. No application runtime or production signing integration is implemented.

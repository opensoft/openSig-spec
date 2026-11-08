# openSig-spec

The **spec leg** of the `opensig` project: requirements, decisions and
acceptance criteria. The implementation lives in
[`opensoft/openSig-code`](https://github.com/opensoft/openSig-code).

**Clone the assembly root, not this repository.** This leg is mounted as a
submodule at `spec/` inside
[`opensoft/openSig`](https://github.com/opensoft/openSig), which
is what pins the commit of this repository that the project currently is:

```sh
git clone --recurse-submodules https://github.com/opensoft/openSig.git
cd openSig
make bootstrap
```

Working here directly is fine — it is an ordinary repository with an ordinary
branch. What advancing this leg does NOT do is advance the project: that is a
commit in the assembly root moving the gitlink, `contracts/spec-pin.yaml` and
any workflow `@<sha>` reference together.

Being the spec leg confers no authority over specifications. The split is
navigation; authority travels in grants, and a project that keeps spec and
code in one repository is reviewed identically.

Topic: `xf-project-opensig`.

## Signing platform design

All original openSig, openAutograph and openCryptograph work is Apache-2.0. Shipped third-party materials must satisfy the MIT/Apache allowlist.

- [Architecture overview](ideation/brainstorm/opensig-overview.md)
- [Implementation plan](ideation/brainstorm/opensig-implementation-plan.md)
- [Triad placement](TRIAD-SETUP.md)

The packet is non-normative brainstorm material. Names and license intent are owner decisions; engine/runtime/provider choices remain open. No signing application is implemented.

<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.8.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.8.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference GitHub Actions using mutable version tags (@v7, @v4) instead of immutable full 40-character SHA digests. This exposes the workflows to supply-chain attacks if the referenced tags are moved or compromised. Affected references include: actions/checkout@v7, actions/setup-node@v7, nick-fields/retry@v4, actions/upload-artifact@v7.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:42`
- `.github/workflows/ci.yml:43`
- `.github/workflows/ci.yml:58`
- `.github/workflows/ci.yml:68`
- `.github/workflows/ci-aocc.yml:29`
- `.github/workflows/ci-aocc.yml:32`
- `.github/workflows/ci-aocc.yml:40`
- `.github/workflows/ci-flang.yml:72`
- `.github/workflows/ci-flang.yml:75`
- `.github/workflows/ci-flang.yml:83`
- `.github/workflows/ci-gfortran.yml:47`
- `.github/workflows/ci-gfortran.yml:50`
- `.github/workflows/ci-gfortran.yml:58`
- `.github/workflows/ci-ifort.yml:72`
- `.github/workflows/ci-ifort.yml:75`
- `.github/workflows/ci-ifort.yml:83`
- `.github/workflows/ci-ifx.yml:100`
- `.github/workflows/ci-ifx.yml:103`
- `.github/workflows/ci-ifx.yml:111`
- `.github/workflows/ci-lfortran.yml:52`
- `.github/workflows/ci-lfortran.yml:55`
- `.github/workflows/ci-lfortran.yml:63`
- `.github/workflows/ci-nvfortran.yml:57`
- `.github/workflows/ci-nvfortran.yml:60`
- `.github/workflows/ci-nvfortran.yml:68`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no individual job defines a `permissions:` key either. This means all jobs run with the default GitHub Actions permissions, which include write access to contents and other scopes, violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/ci-aocc.yml:1`
- `.github/workflows/ci-flang.yml:1`
- `.github/workflows/ci-gfortran.yml:1`
- `.github/workflows/ci-ifort.yml:1`
- `.github/workflows/ci-ifx.yml:1`
- `.github/workflows/ci-lfortran.yml:1`
- `.github/workflows/ci-nvfortran.yml:1`

### script-injection (severity: high)

Sub-rule (a): The 'Verify installation' run: block in every CI workflow directly interpolates ${{ steps.setup.outputs.version }}, ${{ steps.setup.outputs.fc }}, ${{ steps.setup.outputs.cc }}, and ${{ steps.setup.outputs.cxx }} inside shell echo commands. These are steps.*.outputs.* context values — workflow-controllable data that flows through YAML template substitution before the shell processes them. An attacker who can influence step outputs (e.g. via a compromised action or a pull_request trigger) could inject arbitrary shell commands. The safe pattern is to pass these values through env: variables and reference them as quoted shell variables (e.g. echo "  Version: $OUTPUT_VERSION").

Locations:

- `.github/workflows/ci-aocc.yml:62`
- `.github/workflows/ci-flang.yml:120`
- `.github/workflows/ci-gfortran.yml:80`
- `.github/workflows/ci-ifort.yml:115`
- `.github/workflows/ci-ifx.yml:183`
- `.github/workflows/ci-lfortran.yml:84`
- `.github/workflows/ci-nvfortran.yml:89`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 3 finding types across 8 workflow files:

1. unpinned-uses: Pinned all action references to full SHA digests with tag comments:
   - actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1 # v7
   - actions/setup-node@v7 → @820762786026740c76f36085b0efc47a31fe5020 # v7
   - nick-fields/retry@v4 → @ad984534de44a9489a53aefd81eb77f87c70dc60 # v4
   - actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a # v7

2. missing-permissions: Added 'permissions: contents: read' at the top level of all 8 workflow files.

3. script-injection: In all 7 integration test workflows, replaced ${{ steps.setup.outputs.version/fc/cc/cxx }} expressions inside run: echo commands with the corresponding env var references ($OUTPUT_VERSION, $OUTPUT_FC, $OUTPUT_CC, $OUTPUT_CXX) that were already defined in the step's env: block.


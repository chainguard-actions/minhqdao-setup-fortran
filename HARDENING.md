<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.6.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of `${{ steps.setup.outputs.* }}` inside `run:` shell commands. In every CI compiler workflow, the 'Verify installation' step echoes step outputs directly into the shell via expressions like `echo "  Version: ${{ steps.setup.outputs.version }}"`. The `steps.*.outputs.*` context is workflow-controllable and flows through YAML template substitution before the shell sees it, enabling script injection if the output contains shell metacharacters.

Locations:

- `.github/workflows/ci-aocc.yml:52`
- `.github/workflows/ci-flang.yml:100`
- `.github/workflows/ci-gfortran.yml:72`
- `.github/workflows/ci-ifort.yml:100`
- `.github/workflows/ci-ifx.yml:148`
- `.github/workflows/ci-lfortran.yml:72`
- `.github/workflows/ci-nvfortran.yml:88`

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags rather than full 40-character commit SHAs. Affected references include: `actions/checkout@v7`, `actions/setup-node@v6`, `nick-fields/retry@v4`, and `actions/upload-artifact@v7`. These can be silently redirected to different code if the upstream tag is moved, enabling supply-chain attacks.

Locations:

- `.github/workflows/ci.yml:10`
- `.github/workflows/ci.yml:11`
- `.github/workflows/ci.yml:25`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:40`
- `.github/workflows/ci.yml:41`
- `.github/workflows/ci.yml:62`
- `.github/workflows/ci-aocc.yml:29`
- `.github/workflows/ci-aocc.yml:31`
- `.github/workflows/ci-flang.yml:77`
- `.github/workflows/ci-flang.yml:79`
- `.github/workflows/ci-gfortran.yml:57`
- `.github/workflows/ci-gfortran.yml:59`
- `.github/workflows/ci-ifort.yml:87`
- `.github/workflows/ci-ifort.yml:89`
- `.github/workflows/ci-ifx.yml:135`
- `.github/workflows/ci-ifx.yml:137`
- `.github/workflows/ci-lfortran.yml:57`
- `.github/workflows/ci-lfortran.yml:59`
- `.github/workflows/ci-nvfortran.yml:73`
- `.github/workflows/ci-nvfortran.yml:75`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no individual job within any workflow defines a `permissions:` key. Without explicit permissions, workflows inherit the default repository token permissions (which may be `write-all` depending on repository settings), granting unnecessarily broad access to the GITHUB_TOKEN.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/ci-aocc.yml:1`
- `.github/workflows/ci-flang.yml:1`
- `.github/workflows/ci-gfortran.yml:1`
- `.github/workflows/ci-ifort.yml:1`
- `.github/workflows/ci-ifx.yml:1`
- `.github/workflows/ci-lfortran.yml:1`
- `.github/workflows/ci-nvfortran.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across 8 workflow files:

1. **script-injection** (7 files): In ci-aocc.yml, ci-flang.yml, ci-gfortran.yml, ci-ifort.yml, ci-ifx.yml, ci-lfortran.yml, ci-nvfortran.yml — the 'Verify installation' step's run: block was using raw ${{ steps.setup.outputs.version/fc/cc/cxx }} expressions directly in echo commands. These were replaced with the env var references $OUTPUT_VERSION, $OUTPUT_FC, $OUTPUT_CC, $OUTPUT_CXX (which were already defined in the step's env: block).

2. **unpinned-uses** (8 files): Pinned all mutable tag references to full commit SHAs:
   - actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1 # v7
   - actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38 # v6
   - nick-fields/retry@v4 → @ad984534de44a9489a53aefd81eb77f87c70dc60 # v4
   - actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a # v7

3. **missing-permissions** (8 files): Added top-level `permissions: contents: read` to all workflow files (ci.yml, ci-aocc.yml, ci-flang.yml, ci-gfortran.yml, ci-ifort.yml, ci-ifx.yml, ci-lfortran.yml, ci-nvfortran.yml). The contents: read permission is the minimum needed for checkout operations.


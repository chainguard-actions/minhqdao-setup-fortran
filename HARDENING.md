<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference external actions using mutable version tags instead of full 40-character SHA commit hashes. Specifically: `actions/checkout@v6`, `actions/setup-node@v6`, `nick-fields/retry@v4`, and `actions/upload-artifact@v7` are all tag-pinned. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling supply-chain attacks.

Locations:

- `.github/workflows/ci-aocc.yml:29`
- `.github/workflows/ci-aocc.yml:32`
- `.github/workflows/ci-flang.yml:78`
- `.github/workflows/ci-flang.yml:81`
- `.github/workflows/ci-gfortran.yml:65`
- `.github/workflows/ci-gfortran.yml:68`
- `.github/workflows/ci-ifort.yml:78`
- `.github/workflows/ci-ifort.yml:81`
- `.github/workflows/ci-ifx.yml:100`
- `.github/workflows/ci-ifx.yml:103`
- `.github/workflows/ci-lfortran.yml:55`
- `.github/workflows/ci-lfortran.yml:58`
- `.github/workflows/ci-nvfortran.yml:57`
- `.github/workflows/ci-nvfortran.yml:60`
- `.github/workflows/ci.yml:10`
- `.github/workflows/ci.yml:11`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:43`
- `.github/workflows/ci.yml:44`
- `.github/workflows/ci.yml:63`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and none of the individual jobs define job-level `permissions:` keys. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/ci-aocc.yml:1`
- `.github/workflows/ci-flang.yml:1`
- `.github/workflows/ci-gfortran.yml:1`
- `.github/workflows/ci-ifort.yml:1`
- `.github/workflows/ci-ifx.yml:1`
- `.github/workflows/ci-lfortran.yml:1`
- `.github/workflows/ci-nvfortran.yml:1`
- `.github/workflows/ci.yml:1`

### script-injection (severity: high)

Sub-rule (a): Every CI workflow file contains a `run:` step that directly interpolates `${{ steps.setup.outputs.version }}` inside a shell command string. The `steps.*.outputs.*` context is listed as an untrusted-input source — its value flows through YAML template substitution before the shell sees it, allowing an attacker who can influence the output value to inject arbitrary shell commands. The offending line in each file is: `run: 'echo "Output: ${{ steps.setup.outputs.version }}"'`

Locations:

- `.github/workflows/ci-aocc.yml:47`
- `.github/workflows/ci-flang.yml:96`
- `.github/workflows/ci-gfortran.yml:83`
- `.github/workflows/ci-ifort.yml:96`
- `.github/workflows/ci-ifx.yml:118`
- `.github/workflows/ci-lfortran.yml:73`
- `.github/workflows/ci-nvfortran.yml:75`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 8 workflow files:

1. **unpinned-uses**: Pinned all 4 external actions to full SHA hashes:
   - actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803
   - actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38
   - nick-fields/retry@v4 → @ad984534de44a9489a53aefd81eb77f87c70dc60
   - actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a

2. **missing-permissions**: Added `permissions: {}` at the top level of all 8 workflow files.

3. **script-injection**: Fixed all 7 instances of direct `${{ steps.setup.outputs.version }}` interpolation in run: steps by moving the expression to an `env:` block (as SETUP_VERSION) and referencing it as `$SETUP_VERSION` in the shell command.


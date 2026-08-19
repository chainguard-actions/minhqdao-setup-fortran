<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across every workflow file use mutable tag-based refs instead of pinned 40-character SHA digests, making the workflows vulnerable to supply-chain attacks if those tags are moved. Failing references include: `actions/checkout@v6`, `actions/download-artifact@v8`, `actions/setup-node@v6`, `actions/upload-artifact@v7`.

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:32`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:49`
- `.github/workflows/ci.yml:50`
- `.github/workflows/ci.yml:65`
- `.github/workflows/ci-aocc.yml:30`
- `.github/workflows/ci-aocc.yml:33`
- `.github/workflows/ci-flang.yml:78`
- `.github/workflows/ci-flang.yml:81`
- `.github/workflows/ci-gfortran.yml:57`
- `.github/workflows/ci-gfortran.yml:60`
- `.github/workflows/ci-ifort.yml:79`
- `.github/workflows/ci-ifort.yml:82`
- `.github/workflows/ci-ifx.yml:105`
- `.github/workflows/ci-ifx.yml:108`
- `.github/workflows/ci-lfortran.yml:55`
- `.github/workflows/ci-lfortran.yml:58`
- `.github/workflows/ci-nvfortran.yml:60`
- `.github/workflows/ci-nvfortran.yml:63`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no individual job defines its own `permissions:` block. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege.

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

Sub-rule (a): A `${{ steps.setup.outputs.version }}` expression is directly interpolated inside a `run:` shell command string in every integration-test job. The offending line is: `run: "echo \"Output: ${{ steps.setup.outputs.version }}\""`. Although `steps.*.outputs.*` is not directly attacker-controlled in these workflows, any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted by the template engine before the shell ever sees it, bypassing shell quoting. If the output value were ever to contain shell metacharacters (e.g. from a compromised dependency), it would be executed by the shell.

Locations:

- `.github/workflows/ci-aocc.yml:43`
- `.github/workflows/ci-flang.yml:91`
- `.github/workflows/ci-gfortran.yml:66`
- `.github/workflows/ci-ifort.yml:87`
- `.github/workflows/ci-ifx.yml:113`
- `.github/workflows/ci-lfortran.yml:68`
- `.github/workflows/ci-nvfortran.yml:73`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 3 findings across 8 workflow files:

1. unpinned-uses: Pinned all 4 action references to full SHA digests with tag comments:
   - actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10
   - actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38
   - actions/download-artifact@v8 → @3e5f45b2cfb9172054b4087a40e8e0b5a5461e7c
   - actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a

2. missing-permissions: Added `permissions: {}` top-level block to all 8 workflow files.

3. script-injection: In all 7 integration-test workflows (ci-aocc, ci-flang, ci-gfortran, ci-ifort, ci-ifx, ci-lfortran, ci-nvfortran), moved `${{ steps.setup.outputs.version }}` from inline run: strings into an env: block as SETUP_VERSION, then referenced it as $SETUP_VERSION in the shell command.


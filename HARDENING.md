<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.5.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ ... }} expression is interpolated directly inside a run: shell command string. In each CI workflow, the 'Verify installed version' step uses: run: 'echo "Output: ${{ steps.setup.outputs.version }}"'. The steps.setup.outputs.version value flows through YAML template substitution before the shell sees it, allowing an attacker who can influence the output value to inject shell metacharacters.

Locations:

- `.github/workflows/ci-aocc.yml:44`
- `.github/workflows/ci-flang.yml:92`
- `.github/workflows/ci-gfortran.yml:65`
- `.github/workflows/ci-ifort.yml:96`
- `.github/workflows/ci-ifx.yml:131`
- `.github/workflows/ci-lfortran.yml:65`
- `.github/workflows/ci-nvfortran.yml:79`

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks. Unpinned references found: actions/checkout@v7, actions/setup-node@v6, nick-fields/retry@v4, actions/upload-artifact@v7. All should be pinned to a full SHA digest (e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4).

Locations:

- `.github/workflows/ci-aocc.yml:29`
- `.github/workflows/ci-flang.yml:72`
- `.github/workflows/ci-gfortran.yml:52`
- `.github/workflows/ci-ifort.yml:89`
- `.github/workflows/ci-ifx.yml:121`
- `.github/workflows/ci-lfortran.yml:55`
- `.github/workflows/ci-nvfortran.yml:68`
- `.github/workflows/ci.yml:11`

### missing-permissions (severity: medium)

None of the workflow files define a top-level or job-level permissions: key. Without explicit permissions, workflows run with the repository's default token permissions, which may be overly broad (e.g. write access to contents, packages, etc.). Each workflow should declare minimal required permissions explicitly.

Locations:

- `.github/workflows/ci-aocc.yml:1`
- `.github/workflows/ci-flang.yml:1`
- `.github/workflows/ci-gfortran.yml:1`
- `.github/workflows/ci-ifort.yml:1`
- `.github/workflows/ci-ifx.yml:1`
- `.github/workflows/ci-lfortran.yml:1`
- `.github/workflows/ci-nvfortran.yml:1`
- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across 8 workflow files:

1. script-injection: Moved `${{ steps.setup.outputs.version }}` out of run: shell strings into env: blocks (SETUP_VERSION variable) in all 7 CI workflow files.

2. unpinned-uses: Pinned all action references to full SHA digests:
   - actions/checkout@v7 → @9c091bb21b7c1c1d1991bb908d89e4e9dddfe3e0
   - actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38
   - nick-fields/retry@v4 → @ad984534de44a9489a53aefd81eb77f87c70dc60
   - actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a

3. missing-permissions: Added `permissions: contents: read` at the top level of all 8 workflow files (ci.yml and all 7 ci-*.yml files).


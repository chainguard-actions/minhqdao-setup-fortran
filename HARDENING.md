<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.3.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across every workflow file use mutable version tags instead of pinned 40-character SHA commit hashes, making the workflows vulnerable to supply-chain attacks if those tags are moved. Affected references include: `actions/checkout@v6`, `actions/setup-node@v6`, `nick-fields/retry@v4`, and `actions/upload-artifact@v7`.

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:29`
- `.github/workflows/ci.yml:43`
- `.github/workflows/ci.yml:44`
- `.github/workflows/ci.yml:62`
- `.github/workflows/ci-aocc.yml:29`
- `.github/workflows/ci-aocc.yml:32`
- `.github/workflows/ci-flang.yml:79`
- `.github/workflows/ci-flang.yml:82`
- `.github/workflows/ci-gfortran.yml:63`
- `.github/workflows/ci-gfortran.yml:66`
- `.github/workflows/ci-ifort.yml:72`
- `.github/workflows/ci-ifort.yml:75`
- `.github/workflows/ci-ifx.yml:88`
- `.github/workflows/ci-ifx.yml:91`
- `.github/workflows/ci-lfortran.yml:52`
- `.github/workflows/ci-lfortran.yml:55`
- `.github/workflows/ci-nvfortran.yml:57`
- `.github/workflows/ci-nvfortran.yml:60`

### missing-permissions (severity: medium)

None of the workflow files define a `permissions:` block at the top level or at the job level. Without explicit permissions, workflows run with the default (potentially broad) token permissions. Every job in every file is missing a permissions declaration.

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

Sub-rule (a): The 'Verify installed version' step in 7 workflow files directly interpolates `${{ steps.setup.outputs.version }}` (a `steps.*` context expression) inside a `run:` shell command string: `run: 'echo "Output: ${{ steps.setup.outputs.version }}"'`. GitHub Actions substitutes the expression before the shell processes it, so a maliciously crafted output value could inject arbitrary shell commands.

Locations:

- `.github/workflows/ci-aocc.yml:43`
- `.github/workflows/ci-flang.yml:97`
- `.github/workflows/ci-gfortran.yml:79`
- `.github/workflows/ci-ifort.yml:88`
- `.github/workflows/ci-ifx.yml:106`
- `.github/workflows/ci-lfortran.yml:68`
- `.github/workflows/ci-nvfortran.yml:76`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 8 workflow files: (1) Pinned all action references to full SHAs: actions/checkout@v6→df4cb1c..., actions/setup-node@v6→249970729..., nick-fields/retry@v4→ad984534..., actions/upload-artifact@v7→043fb46d... (2) Added 'permissions: contents: read' at the top level of all 8 workflow files. (3) Fixed script injection in 7 workflow files by moving '${{ steps.setup.outputs.version }}' from run: shell strings into step env: blocks as SETUP_VERSION, then referencing it as $SETUP_VERSION in the shell command.


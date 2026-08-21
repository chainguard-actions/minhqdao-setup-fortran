<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.10.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.10.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files use mutable tag-based `uses:` references instead of full 40-character SHA commit hashes. This exposes the workflows to supply-chain attacks if the referenced action tags are moved or compromised. Failing references include: `actions/checkout@v7`, `nick-fields/retry@v4`, `actions/setup-node@v7`, `actions/upload-artifact@v7`, `actions/github-script@v9`.

Locations:

- `.github/workflows/ci-aocc.yml:29`
- `.github/workflows/ci-armflang.yml:22`
- `.github/workflows/ci-flang.yml:72`
- `.github/workflows/ci-gfortran.yml:48`
- `.github/workflows/ci-ifort.yml:68`
- `.github/workflows/ci-ifx.yml:79`
- `.github/workflows/ci-lfortran.yml:47`
- `.github/workflows/ci-nvfortran.yml:52`
- `.github/workflows/ci.yml:42`
- `.github/workflows/validation.yml:14`

### script-injection (severity: high)

Sub-rule (a): `${{ steps.setup.outputs.* }}` expressions are interpolated directly inside `run:` shell command strings via `echo` statements. The `steps.*.outputs.*` context is workflow-controllable and is substituted into the shell command before execution, allowing a compromised or attacker-controlled step output to inject arbitrary shell commands. Offending lines include e.g. `echo "  Version: ${{ steps.setup.outputs.version }}"`, `echo "  FC:      ${{ steps.setup.outputs.fc }}"`, `echo "  CC:      ${{ steps.setup.outputs.cc }}"`, `echo "  CXX:     ${{ steps.setup.outputs.cxx }}"`. These values should be passed via `env:` variables and referenced as `$OUTPUT_VERSION`, `$OUTPUT_FC`, etc. (which are already set in the `env:` block of the same step).

Locations:

- `.github/workflows/ci-aocc.yml:67`
- `.github/workflows/ci-flang.yml:100`
- `.github/workflows/ci-gfortran.yml:72`
- `.github/workflows/ci-ifort.yml:107`
- `.github/workflows/ci-ifx.yml:152`
- `.github/workflows/ci-lfortran.yml:71`
- `.github/workflows/ci-nvfortran.yml:79`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all unpinned action references across 10 workflow files by pinning to full SHA hashes: actions/checkout@v7→SHA 3d3c42e5, nick-fields/retry@v4→SHA ad984534, actions/setup-node@v7→SHA 82076278, actions/upload-artifact@v7→SHA 043fb46d, actions/github-script@v9→SHA 3a2844b7. Fixed script-injection in 7 workflow files (ci-aocc.yml, ci-flang.yml, ci-gfortran.yml, ci-ifort.yml, ci-ifx.yml, ci-lfortran.yml, ci-nvfortran.yml) by replacing ${{ steps.setup.outputs.version/fc/cc/cxx }} expressions in run: shell strings with the already-defined env var references $OUTPUT_VERSION, $OUTPUT_FC, $OUTPUT_CC, $OUTPUT_CXX.


<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.9.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.9.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files use mutable tag-based refs (e.g. @v7, @v4) instead of pinned 40-character SHA digests. Affected actions: actions/checkout@v7, actions/setup-node@v7, nick-fields/retry@v4, actions/upload-artifact@v7. Any of these tags could be moved to point to malicious code without notice.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:31`
- `.github/workflows/ci.yml:47`
- `.github/workflows/ci.yml:48`
- `.github/workflows/ci.yml:63`
- `.github/workflows/ci.yml:68`
- `.github/workflows/ci-aocc.yml:33`
- `.github/workflows/ci-aocc.yml:36`
- `.github/workflows/ci-aocc.yml:46`
- `.github/workflows/ci-armflang.yml:27`
- `.github/workflows/ci-armflang.yml:30`
- `.github/workflows/ci-armflang.yml:40`
- `.github/workflows/ci-flang.yml:83`
- `.github/workflows/ci-flang.yml:86`
- `.github/workflows/ci-flang.yml:96`
- `.github/workflows/ci-gfortran.yml:54`
- `.github/workflows/ci-gfortran.yml:57`
- `.github/workflows/ci-gfortran.yml:67`
- `.github/workflows/ci-ifort.yml:72`
- `.github/workflows/ci-ifort.yml:75`
- `.github/workflows/ci-ifort.yml:85`
- `.github/workflows/ci-ifx.yml:82`
- `.github/workflows/ci-ifx.yml:85`
- `.github/workflows/ci-ifx.yml:95`
- `.github/workflows/ci-lfortran.yml:57`
- `.github/workflows/ci-lfortran.yml:60`
- `.github/workflows/ci-lfortran.yml:70`
- `.github/workflows/ci-nvfortran.yml:57`
- `.github/workflows/ci-nvfortran.yml:60`
- `.github/workflows/ci-nvfortran.yml:70`

### missing-permissions (severity: medium)

None of the workflow files declare a top-level or job-level `permissions:` block. Without explicit permissions, jobs run with the default (potentially write) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/ci-aocc.yml:1`
- `.github/workflows/ci-armflang.yml:1`
- `.github/workflows/ci-flang.yml:1`
- `.github/workflows/ci-gfortran.yml:1`
- `.github/workflows/ci-ifort.yml:1`
- `.github/workflows/ci-ifx.yml:1`
- `.github/workflows/ci-lfortran.yml:1`
- `.github/workflows/ci-nvfortran.yml:1`

### script-injection (severity: high)

Sub-rule (a): `${{ steps.setup.outputs.* }}` expressions are interpolated directly inside `run:` shell command strings in the 'Verify installation' step across multiple workflow files. For example: `echo "  Version: ${{ steps.setup.outputs.version }}"`. Although `steps.*.outputs.*` values are set by the action itself, any expression inside a `run:` block undergoes YAML template substitution before the shell sees it, making it a script-injection risk. The values should be passed via `env:` variables (already done for OUTPUT_VERSION etc.) and the direct `${{ }}` interpolations inside the run block should be replaced with the corresponding `$OUTPUT_VERSION`, `$OUTPUT_FC`, `$OUTPUT_CC`, `$OUTPUT_CXX` env var references.

Locations:

- `.github/workflows/ci-aocc.yml:71`
- `.github/workflows/ci-flang.yml:113`
- `.github/workflows/ci-gfortran.yml:93`
- `.github/workflows/ci-ifort.yml:113`
- `.github/workflows/ci-ifx.yml:123`
- `.github/workflows/ci-lfortran.yml:98`
- `.github/workflows/ci-nvfortran.yml:98`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 9 workflow files in hardened/action/.github/workflows/:

1. unpinned-uses: Pinned all action references to full SHA digests:
   - actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1 # v7
   - actions/setup-node@v7 → @820762786026740c76f36085b0efc47a31fe5020 # v7
   - nick-fields/retry@v4 → @ad984534de44a9489a53aefd81eb77f87c70dc60 # v4
   - actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a # v7

2. missing-permissions: Added `permissions: contents: read` top-level block to all 9 workflow files (ci.yml, ci-aocc.yml, ci-armflang.yml, ci-flang.yml, ci-gfortran.yml, ci-ifort.yml, ci-ifx.yml, ci-lfortran.yml, ci-nvfortran.yml).

3. script-injection: Replaced direct `${{ steps.setup.outputs.version/fc/cc/cxx }}` interpolations inside run: blocks with `$OUTPUT_VERSION`, `$OUTPUT_FC`, `$OUTPUT_CC`, `$OUTPUT_CXX` env var references in 7 files (ci-aocc.yml, ci-flang.yml, ci-gfortran.yml, ci-ifort.yml, ci-ifx.yml, ci-lfortran.yml, ci-nvfortran.yml). The env: blocks mapping step outputs to these variables were already present and preserved.


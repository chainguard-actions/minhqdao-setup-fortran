<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.7.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files use mutable tag-based refs instead of pinned 40-character SHA commits. Affected references include: actions/checkout@v7, actions/setup-node@v7, actions/upload-artifact@v7, and nick-fields/retry@v4. A tag can be silently moved to point to a different (potentially malicious) commit, enabling supply-chain attacks.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:29`
- `.github/workflows/ci.yml:44`
- `.github/workflows/ci.yml:45`
- `.github/workflows/ci.yml:60`
- `.github/workflows/ci.yml:65`
- `.github/workflows/ci-aocc.yml:29`
- `.github/workflows/ci-aocc.yml:37`
- `.github/workflows/ci-aocc.yml:47`
- `.github/workflows/ci-flang.yml:72`
- `.github/workflows/ci-flang.yml:80`
- `.github/workflows/ci-flang.yml:90`
- `.github/workflows/ci-gfortran.yml:55`
- `.github/workflows/ci-gfortran.yml:63`
- `.github/workflows/ci-gfortran.yml:73`
- `.github/workflows/ci-ifort.yml:79`
- `.github/workflows/ci-ifort.yml:87`
- `.github/workflows/ci-ifort.yml:97`
- `.github/workflows/ci-ifx.yml:113`
- `.github/workflows/ci-ifx.yml:121`
- `.github/workflows/ci-ifx.yml:131`
- `.github/workflows/ci-lfortran.yml:57`
- `.github/workflows/ci-lfortran.yml:65`
- `.github/workflows/ci-lfortran.yml:75`
- `.github/workflows/ci-nvfortran.yml:68`
- `.github/workflows/ci-nvfortran.yml:76`
- `.github/workflows/ci-nvfortran.yml:86`

### permissions (severity: medium)

missing-permissions: None of the workflow files define a top-level 'permissions:' key, and no job within any workflow defines job-level permissions. Without explicit permissions, workflows inherit the default repository token permissions (which may be write-all depending on repository settings), granting unnecessarily broad access.

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

Sub-rule (a): Every CI workflow file contains a 'Verify installation' run: block that directly interpolates ${{ steps.setup.outputs.version }}, ${{ steps.setup.outputs.fc }}, ${{ steps.setup.outputs.cc }}, and ${{ steps.setup.outputs.cxx }} into shell echo commands. The steps.*.outputs.* context is workflow-controllable and is substituted by the GitHub Actions template engine before the shell sees the string, allowing an attacker who can influence action outputs to inject arbitrary shell commands. Example offending lines: `echo "  Version: ${{ steps.setup.outputs.version }}"`, `echo "  FC:      ${{ steps.setup.outputs.fc }}"`.

Locations:

- `.github/workflows/ci-aocc.yml:62`
- `.github/workflows/ci-aocc.yml:63`
- `.github/workflows/ci-aocc.yml:64`
- `.github/workflows/ci-aocc.yml:65`
- `.github/workflows/ci-flang.yml:107`
- `.github/workflows/ci-flang.yml:108`
- `.github/workflows/ci-flang.yml:109`
- `.github/workflows/ci-flang.yml:110`
- `.github/workflows/ci-gfortran.yml:83`
- `.github/workflows/ci-gfortran.yml:84`
- `.github/workflows/ci-gfortran.yml:85`
- `.github/workflows/ci-gfortran.yml:86`
- `.github/workflows/ci-ifort.yml:107`
- `.github/workflows/ci-ifort.yml:108`
- `.github/workflows/ci-ifort.yml:109`
- `.github/workflows/ci-ifort.yml:110`
- `.github/workflows/ci-ifx.yml:145`
- `.github/workflows/ci-ifx.yml:146`
- `.github/workflows/ci-ifx.yml:147`
- `.github/workflows/ci-ifx.yml:148`
- `.github/workflows/ci-lfortran.yml:87`
- `.github/workflows/ci-lfortran.yml:88`
- `.github/workflows/ci-lfortran.yml:89`
- `.github/workflows/ci-lfortran.yml:90`
- `.github/workflows/ci-nvfortran.yml:97`
- `.github/workflows/ci-nvfortran.yml:98`
- `.github/workflows/ci-nvfortran.yml:99`
- `.github/workflows/ci-nvfortran.yml:100`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, script-injection

**Notes:**

Fixed all 3 findings across 8 workflow files:

1. unpinned-uses: Pinned all 4 action references to full 40-char SHAs with tag comments preserved:
   - actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1
   - actions/setup-node@v7 → @820762786026740c76f36085b0efc47a31fe5020
   - actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a
   - nick-fields/retry@v4 → @ad984534de44a9489a53aefd81eb77f87c70dc60

2. permissions: Added top-level 'permissions: contents: read' block to all 8 workflow files.

3. script-injection: In all 7 integration-test workflow files, replaced direct ${{ steps.setup.outputs.* }} interpolations in echo commands with references to the already-defined env vars ($OUTPUT_VERSION, $OUTPUT_FC, $OUTPUT_CC, $OUTPUT_CXX). The env: block was already present mapping those outputs to env vars, so only the run: shell commands needed updating.


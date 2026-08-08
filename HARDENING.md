<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.9.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across every workflow file use mutable tag refs (e.g. `@v7`, `@v4`) instead of pinned 40-character SHA commit hashes. This exposes the action to supply-chain attacks if the referenced tags are moved or overwritten. Affected references include: `actions/checkout@v7`, `actions/setup-node@v7`, `nick-fields/retry@v4`, `actions/upload-artifact@v7`, and the reusable workflow call `./.github/workflows/ci.yml` (no ref).

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:29`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:45`
- `.github/workflows/ci.yml:46`
- `.github/workflows/ci.yml:65`
- `.github/workflows/ci.yml:73`
- `.github/workflows/ci-aocc.yml:29`
- `.github/workflows/ci-aocc.yml:33`
- `.github/workflows/ci-aocc.yml:40`
- `.github/workflows/ci-armflang.yml:27`
- `.github/workflows/ci-armflang.yml:31`
- `.github/workflows/ci-armflang.yml:38`
- `.github/workflows/ci-flang.yml:82`
- `.github/workflows/ci-flang.yml:86`
- `.github/workflows/ci-flang.yml:93`
- `.github/workflows/ci-gfortran.yml:52`
- `.github/workflows/ci-gfortran.yml:56`
- `.github/workflows/ci-gfortran.yml:63`
- `.github/workflows/ci-ifort.yml:82`
- `.github/workflows/ci-ifort.yml:86`
- `.github/workflows/ci-ifort.yml:93`
- `.github/workflows/ci-ifx.yml:110`
- `.github/workflows/ci-ifx.yml:114`
- `.github/workflows/ci-ifx.yml:121`
- `.github/workflows/ci-lfortran.yml:55`
- `.github/workflows/ci-lfortran.yml:59`
- `.github/workflows/ci-lfortran.yml:66`
- `.github/workflows/ci-nvfortran.yml:57`
- `.github/workflows/ci-nvfortran.yml:61`
- `.github/workflows/ci-nvfortran.yml:68`

### missing-permissions (severity: medium)

None of the 9 workflow files define a top-level `permissions:` key, and none of their jobs define job-level `permissions:` blocks. Without explicit permissions, GitHub Actions grants the default token permissions (which can be `write-all` on some repository configurations), violating the principle of least privilege.

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

Rule (a) violation: Multiple `run:` blocks in the 'Verify installation' steps directly interpolate `${{ steps.setup.outputs.version }}`, `${{ steps.setup.outputs.fc }}`, `${{ steps.setup.outputs.cc }}`, and `${{ steps.setup.outputs.cxx }}` expressions inside shell `echo` commands. Any `${{ ... }}` expression is substituted into the shell script string by the Actions runner before the shell executes it, meaning a malicious value in these outputs could inject arbitrary shell commands. The values should be passed via `env:` variables (which are already done for the `npm run verify-installation` call) and referenced as `$OUTPUT_VERSION` etc. in the echo statements instead. Affected lines include e.g.: `echo "  Version: ${{ steps.setup.outputs.version }}"`

Locations:

- `.github/workflows/ci-aocc.yml:68`
- `.github/workflows/ci-aocc.yml:69`
- `.github/workflows/ci-aocc.yml:70`
- `.github/workflows/ci-aocc.yml:71`
- `.github/workflows/ci-armflang.yml:56`
- `.github/workflows/ci-flang.yml:107`
- `.github/workflows/ci-flang.yml:108`
- `.github/workflows/ci-flang.yml:109`
- `.github/workflows/ci-flang.yml:110`
- `.github/workflows/ci-gfortran.yml:79`
- `.github/workflows/ci-gfortran.yml:80`
- `.github/workflows/ci-gfortran.yml:81`
- `.github/workflows/ci-gfortran.yml:82`
- `.github/workflows/ci-ifort.yml:107`
- `.github/workflows/ci-ifort.yml:108`
- `.github/workflows/ci-ifort.yml:109`
- `.github/workflows/ci-ifort.yml:110`
- `.github/workflows/ci-ifx.yml:135`
- `.github/workflows/ci-ifx.yml:136`
- `.github/workflows/ci-ifx.yml:137`
- `.github/workflows/ci-ifx.yml:138`
- `.github/workflows/ci-lfortran.yml:82`
- `.github/workflows/ci-lfortran.yml:83`
- `.github/workflows/ci-lfortran.yml:84`
- `.github/workflows/ci-lfortran.yml:85`
- `.github/workflows/ci-nvfortran.yml:80`
- `.github/workflows/ci-nvfortran.yml:81`
- `.github/workflows/ci-nvfortran.yml:82`
- `.github/workflows/ci-nvfortran.yml:83`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 9 workflow files: (1) Pinned all action references to full SHA hashes: actions/checkout@v7→3d3c42e5..., actions/setup-node@v7→820762786..., nick-fields/retry@v4→ad984534..., actions/upload-artifact@v7→043fb46d.... (2) Added 'permissions: contents: read' top-level block to all 9 workflow files. (3) Replaced ${{ steps.setup.outputs.version/fc/cc/cxx }} expressions in run: echo statements with $OUTPUT_VERSION/$OUTPUT_FC/$OUTPUT_CC/$OUTPUT_CXX env vars (which were already defined in the step's env: block) in ci-aocc.yml, ci-flang.yml, ci-gfortran.yml, ci-ifort.yml, ci-ifx.yml, ci-lfortran.yml, and ci-nvfortran.yml.


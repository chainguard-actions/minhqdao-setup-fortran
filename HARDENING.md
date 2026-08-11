<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.9.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.9.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable version tags instead of full 40-character commit SHAs. Unpinned actions are vulnerable to supply-chain attacks if the tag is moved. Affected references include: `actions/checkout@v7`, `actions/setup-node@v7`, `nick-fields/retry@v4`, and `actions/upload-artifact@v7`. All should be pinned to their full SHA digest (e.g. `actions/checkout@<40-char-sha> # v7`).

Locations:

- `.github/workflows/ci-aocc.yml:33`
- `.github/workflows/ci-aocc.yml:36`
- `.github/workflows/ci-aocc.yml:44`
- `.github/workflows/ci-armflang.yml:28`
- `.github/workflows/ci-armflang.yml:31`
- `.github/workflows/ci-armflang.yml:39`
- `.github/workflows/ci-flang.yml:88`
- `.github/workflows/ci-flang.yml:91`
- `.github/workflows/ci-flang.yml:99`
- `.github/workflows/ci-gfortran.yml:55`
- `.github/workflows/ci-gfortran.yml:58`
- `.github/workflows/ci-gfortran.yml:66`
- `.github/workflows/ci-ifort.yml:108`
- `.github/workflows/ci-ifort.yml:111`
- `.github/workflows/ci-ifort.yml:119`
- `.github/workflows/ci-ifx.yml:173`
- `.github/workflows/ci-ifx.yml:176`
- `.github/workflows/ci-ifx.yml:184`
- `.github/workflows/ci-lfortran.yml:57`
- `.github/workflows/ci-lfortran.yml:60`
- `.github/workflows/ci-lfortran.yml:68`
- `.github/workflows/ci-nvfortran.yml:56`
- `.github/workflows/ci-nvfortran.yml:59`
- `.github/workflows/ci-nvfortran.yml:67`
- `.github/workflows/ci.yml:10`
- `.github/workflows/ci.yml:11`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:31`
- `.github/workflows/ci.yml:50`
- `.github/workflows/ci.yml:51`
- `.github/workflows/ci.yml:70`
- `.github/workflows/ci.yml:76`

### missing-permissions (severity: medium)

None of the 9 workflow files define a top-level `permissions:` key, and no individual job defines its own `permissions:` block. Without explicit permissions, workflows run with the default (potentially broad) token permissions. Each workflow should declare minimal required permissions (e.g. `permissions: read-all` or specific scopes like `contents: read`).

Locations:

- `.github/workflows/ci-aocc.yml:1`
- `.github/workflows/ci-armflang.yml:1`
- `.github/workflows/ci-flang.yml:1`
- `.github/workflows/ci-gfortran.yml:1`
- `.github/workflows/ci-ifort.yml:1`
- `.github/workflows/ci-ifx.yml:1`
- `.github/workflows/ci-lfortran.yml:1`
- `.github/workflows/ci-nvfortran.yml:1`
- `.github/workflows/ci.yml:1`

### script-injection (severity: high)

Sub-rule (a): GitHub Actions expressions (`${{ steps.setup.outputs.* }}`) are directly interpolated inside `run:` shell command strings in the 'Verify installation' step across multiple workflow files. The values `steps.setup.outputs.version`, `steps.setup.outputs.fc`, `steps.setup.outputs.cc`, and `steps.setup.outputs.cxx` are substituted into the shell script before the shell parses it, allowing injection of shell metacharacters if the action outputs contain attacker-influenced content. These values should be passed via `env:` variables (which are already present) and referenced as `$OUTPUT_VERSION`, `$OUTPUT_FC`, etc. inside the `run:` block instead of using `${{ ... }}` expressions directly. Offending lines: `echo "  Version: ${{ steps.setup.outputs.version }}"`, `echo "  FC:      ${{ steps.setup.outputs.fc }}"`, `echo "  CC:      ${{ steps.setup.outputs.cc }}"`, `echo "  CXX:     ${{ steps.setup.outputs.cxx }}"`.

Locations:

- `.github/workflows/ci-aocc.yml:66`
- `.github/workflows/ci-aocc.yml:67`
- `.github/workflows/ci-aocc.yml:68`
- `.github/workflows/ci-aocc.yml:69`
- `.github/workflows/ci-flang.yml:131`
- `.github/workflows/ci-flang.yml:132`
- `.github/workflows/ci-flang.yml:133`
- `.github/workflows/ci-flang.yml:134`
- `.github/workflows/ci-gfortran.yml:80`
- `.github/workflows/ci-gfortran.yml:81`
- `.github/workflows/ci-gfortran.yml:82`
- `.github/workflows/ci-gfortran.yml:83`
- `.github/workflows/ci-ifort.yml:140`
- `.github/workflows/ci-ifort.yml:141`
- `.github/workflows/ci-ifort.yml:142`
- `.github/workflows/ci-ifort.yml:143`
- `.github/workflows/ci-ifx.yml:213`
- `.github/workflows/ci-ifx.yml:214`
- `.github/workflows/ci-ifx.yml:215`
- `.github/workflows/ci-ifx.yml:216`
- `.github/workflows/ci-lfortran.yml:83`
- `.github/workflows/ci-lfortran.yml:84`
- `.github/workflows/ci-lfortran.yml:85`
- `.github/workflows/ci-lfortran.yml:86`
- `.github/workflows/ci-nvfortran.yml:103`
- `.github/workflows/ci-nvfortran.yml:104`
- `.github/workflows/ci-nvfortran.yml:105`
- `.github/workflows/ci-nvfortran.yml:106`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 9 workflow files: (1) Pinned all action references to full 40-char SHAs: actions/checkout@v7→@3d3c42e5aac5ba805825da76410c181273ba90b1, actions/setup-node@v7→@820762786026740c76f36085b0efc47a31fe5020, nick-fields/retry@v4→@ad984534de44a9489a53aefd81eb77f87c70dc60, actions/upload-artifact@v7→@043fb46d1a93c77aae656e7c1c64a875d1fc6a0a. (2) Added top-level `permissions: contents: read` to all 9 workflow files. (3) Replaced `${{ steps.setup.outputs.version/fc/cc/cxx }}` expressions directly in run: shell strings with the corresponding $OUTPUT_VERSION/$OUTPUT_FC/$OUTPUT_CC/$OUTPUT_CXX environment variables that were already defined in the env: blocks of those steps.


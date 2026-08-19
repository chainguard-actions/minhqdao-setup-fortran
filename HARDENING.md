<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.3.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.3.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use tag-based (non-SHA) `uses:` references, which are mutable and vulnerable to supply-chain attacks. All references should be pinned to a full 40-character commit SHA.

ci.yml: actions/checkout@v6, actions/setup-node@v6 (×3), actions/upload-artifact@v7
ci-aocc.yml: actions/checkout@v6, nick-fields/retry@v4
ci-flang.yml: actions/checkout@v6, nick-fields/retry@v4
ci-gfortran.yml: actions/checkout@v6, nick-fields/retry@v4
ci-ifort.yml: actions/checkout@v6, nick-fields/retry@v4
ci-ifx.yml: actions/checkout@v6, nick-fields/retry@v4
ci-lfortran.yml: actions/checkout@v6, nick-fields/retry@v4
ci-nvfortran.yml: actions/checkout@v6, nick-fields/retry@v4

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
- `.github/workflows/ci-gfortran.yml:62`
- `.github/workflows/ci-gfortran.yml:65`
- `.github/workflows/ci-ifort.yml:79`
- `.github/workflows/ci-ifort.yml:82`
- `.github/workflows/ci-ifx.yml:109`
- `.github/workflows/ci-ifx.yml:112`
- `.github/workflows/ci-lfortran.yml:55`
- `.github/workflows/ci-lfortran.yml:58`
- `.github/workflows/ci-nvfortran.yml:60`
- `.github/workflows/ci-nvfortran.yml:63`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` block, and no job within any file defines its own `permissions:` block. Without explicit permissions, workflows inherit the default repository token permissions, which may be overly broad (write access to contents, packages, etc.). Each workflow should declare minimal required permissions.

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

Sub-rule (a): Seven workflow files contain a `run:` step that directly interpolates a `${{ ... }}` expression inside the shell command string. Specifically, `run: 'echo "Output: ${{ steps.setup.outputs.version }}"'` embeds `${{ steps.setup.outputs.version }}` directly in the shell command before the shell ever sees it. Although `steps.*.outputs.*` is less directly attacker-controlled than `github.event.*`, any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted into the shell command string by the Actions template engine before the shell parses it. The value should be passed via an `env:` variable and referenced as `"$ENV_VAR"` instead.

Locations:

- `.github/workflows/ci-aocc.yml:47`
- `.github/workflows/ci-flang.yml:97`
- `.github/workflows/ci-gfortran.yml:80`
- `.github/workflows/ci-ifort.yml:97`
- `.github/workflows/ci-ifx.yml:127`
- `.github/workflows/ci-lfortran.yml:73`
- `.github/workflows/ci-nvfortran.yml:78`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 3 findings across 8 workflow files:

1. unpinned-uses: Pinned all action references to full SHAs with tag comments:
   - actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 # v6
   - actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38 # v6
   - actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a # v7
   - nick-fields/retry@v4 → @ad984534de44a9489a53aefd81eb77f87c70dc60 # v4

2. missing-permissions: Added `permissions: contents: read` top-level block to all 8 workflow files.

3. script-injection: In 7 workflow files, moved `${{ steps.setup.outputs.version }}` out of the `run:` shell string into an `env:` block as `SETUP_VERSION`, then referenced it as `$SETUP_VERSION` in the shell command.


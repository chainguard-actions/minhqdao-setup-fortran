<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.1.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: Every CI integration-test workflow directly interpolates a `${{ steps.setup.outputs.version }}` expression inside a `run:` shell command string: `run: "echo \"Output: ${{ steps.setup.outputs.version }}\""`. Any expression inside a `run:` block is evaluated by the YAML template engine before the shell sees it, enabling script injection if the value contains shell metacharacters. The value should be passed via an `env:` variable and then double-quoted in the shell script instead.

Locations:

- `.github/workflows/ci-aocc.yml:43`
- `.github/workflows/ci-flang.yml:78`
- `.github/workflows/ci-gfortran.yml:72`
- `.github/workflows/ci-ifort.yml:87`
- `.github/workflows/ci-ifx.yml:111`
- `.github/workflows/ci-lfortran.yml:60`
- `.github/workflows/ci-nvfortran.yml:68`

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags instead of immutable 40-character SHA digests, making them vulnerable to supply-chain attacks if the tag is moved. Unpinned references found: `actions/checkout@v6`, `actions/setup-node@v6`, `actions/download-artifact@v8`, `actions/upload-artifact@v7`. All should be pinned to a full commit SHA (e.g. `actions/checkout@<40-hex-sha> # v6`).

Locations:

- `.github/workflows/ci-aocc.yml:28`
- `.github/workflows/ci-aocc.yml:31`
- `.github/workflows/ci-flang.yml:63`
- `.github/workflows/ci-flang.yml:66`
- `.github/workflows/ci-gfortran.yml:57`
- `.github/workflows/ci-gfortran.yml:60`
- `.github/workflows/ci-ifort.yml:72`
- `.github/workflows/ci-ifort.yml:75`
- `.github/workflows/ci-ifx.yml:96`
- `.github/workflows/ci-ifx.yml:99`
- `.github/workflows/ci-lfortran.yml:45`
- `.github/workflows/ci-lfortran.yml:48`
- `.github/workflows/ci-nvfortran.yml:54`
- `.github/workflows/ci-nvfortran.yml:57`
- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:42`
- `.github/workflows/ci.yml:43`
- `.github/workflows/ci.yml:62`

### missing-permissions (severity: medium)

None of the 8 workflow files under `.github/workflows/` declare a top-level `permissions:` key, and no individual job declares its own `permissions:` block. Without explicit permissions, jobs run with the repository's default token permissions, which may be broader than necessary (e.g. `write` on `contents`). Each workflow or each job should declare the minimal required permissions (e.g. `permissions: read-all` or specific scopes).

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

Fixed all 3 findings across 8 workflow files:

1. script-injection: In all 7 CI integration-test workflow files, moved `${{ steps.setup.outputs.version }}` out of the `run:` shell string into an `env:` block as `SETUP_VERSION`, then referenced it as `$SETUP_VERSION` in the shell script.

2. unpinned-uses: Pinned all 4 unpinned actions to full 40-character commit SHAs with tag comments:
   - actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 # v6
   - actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38 # v6
   - actions/download-artifact@v8 → @3e5f45b2cfb9172054b4087a40e8e0b5a5461e7c # v8
   - actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a # v7

3. missing-permissions: Added `permissions: contents: read` top-level block to all 8 workflow files, granting only the minimum permissions needed for checkout operations.


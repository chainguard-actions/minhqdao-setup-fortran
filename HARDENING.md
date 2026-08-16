<!-- markdownlint-disable -->

# Hardening Report: minhqdao--setup-fortran/v1.9.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **minhqdao--setup-fortran/v1.9.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tag refs instead of pinned full-length SHA commit hashes. This exposes the workflow to supply-chain attacks if the tag is moved or the upstream repository is compromised.

Failing references:
- `actions/checkout@v7` (all CI workflow files and validation.yml)
- `nick-fields/retry@v4` (all CI workflow files)
- `actions/setup-node@v7` (validation.yml)
- `actions/upload-artifact@v7` (validation.yml)

All of these must be replaced with their full 40-character commit SHA, e.g. `actions/checkout@<sha> # v7`.

Locations:

- `.github/workflows/ci-aocc.yml:29`
- `.github/workflows/ci-aocc.yml:32`
- `.github/workflows/ci-aocc.yml:42`
- `.github/workflows/ci-armflang.yml:22`
- `.github/workflows/ci-armflang.yml:25`
- `.github/workflows/ci-armflang.yml:33`
- `.github/workflows/ci-flang.yml:79`
- `.github/workflows/ci-flang.yml:82`
- `.github/workflows/ci-flang.yml:92`
- `.github/workflows/ci-gfortran.yml:50`
- `.github/workflows/ci-gfortran.yml:53`
- `.github/workflows/ci-gfortran.yml:63`
- `.github/workflows/ci-ifort.yml:96`
- `.github/workflows/ci-ifort.yml:99`
- `.github/workflows/ci-ifort.yml:109`
- `.github/workflows/ci-ifx.yml:160`
- `.github/workflows/ci-ifx.yml:163`
- `.github/workflows/ci-ifx.yml:173`
- `.github/workflows/ci-lfortran.yml:56`
- `.github/workflows/ci-lfortran.yml:59`
- `.github/workflows/ci-lfortran.yml:69`
- `.github/workflows/ci-nvfortran.yml:55`
- `.github/workflows/ci-nvfortran.yml:58`
- `.github/workflows/ci-nvfortran.yml:68`
- `.github/workflows/validation.yml:10`
- `.github/workflows/validation.yml:11`
- `.github/workflows/validation.yml:30`
- `.github/workflows/validation.yml:31`
- `.github/workflows/validation.yml:48`
- `.github/workflows/validation.yml:49`
- `.github/workflows/validation.yml:76`
- `.github/workflows/validation.yml:88`

### script-injection (severity: high)

Multiple workflow files directly interpolate `${{ steps.setup.outputs.* }}` expressions inside `run:` shell command strings (rule a). The values `steps.setup.outputs.version`, `steps.setup.outputs.fc`, `steps.setup.outputs.cc`, and `steps.setup.outputs.cxx` are passed through YAML template substitution before the shell sees them, meaning any newlines or shell metacharacters in those values would be interpreted by the shell. These should be passed via `env:` variables (which are already present in the same step) and referenced as `$OUTPUT_VERSION`, `$OUTPUT_FC`, etc., rather than re-interpolated with `${{ }}` inside the run block.

Offending lines in each file (inside the 'Verify installation' step's run: block):
```
echo "  Version: ${{ steps.setup.outputs.version }}"
echo "  FC:      ${{ steps.setup.outputs.fc }}"
echo "  CC:      ${{ steps.setup.outputs.cc }}"
echo "  CXX:     ${{ steps.setup.outputs.cxx }}"
```

Locations:

- `.github/workflows/ci-aocc.yml:67`
- `.github/workflows/ci-flang.yml:121`
- `.github/workflows/ci-gfortran.yml:89`
- `.github/workflows/ci-ifort.yml:130`
- `.github/workflows/ci-ifx.yml:178`
- `.github/workflows/ci-lfortran.yml:82`
- `.github/workflows/ci-nvfortran.yml:103`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all unpinned action references by pinning to full SHA hashes: actions/checkout@v7→3d3c42e5..., nick-fields/retry@v4→ad984534..., actions/setup-node@v7→820762786..., actions/upload-artifact@v7→043fb46d... across all 9 workflow files. Fixed script-injection in 7 CI workflow files by replacing ${{ steps.setup.outputs.version/fc/cc/cxx }} expressions inside run: blocks with $OUTPUT_VERSION/$OUTPUT_FC/$OUTPUT_CC/$OUTPUT_CXX environment variable references (the env: block already correctly maps the expressions to these variables).


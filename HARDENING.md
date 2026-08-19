<!-- markdownlint-disable -->

# Hardening Report: pdm-project--setup-pdm/v4.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **pdm-project--setup-pdm/v4.4** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses actions/checkout@v3, which is pinned to a mutable tag rather than an immutable 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved.

Locations:

- `.github/workflows/ci.yml:16`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and no job-level `permissions:` key on the Testing job. Without explicit permissions, the workflow inherits the repository default (often write-all), granting broader access than necessary.

Locations:

- `.github/workflows/ci.yml:1`

### script-injection (severity: high)

Sub-rule (a): The 'Check output' run: block directly interpolates GitHub Actions expressions inside shell commands: `echo ${{ steps.setup-pdm.outputs.pdm-bin }}`, `echo ${{ steps.setup-pdm.outputs.pdm-version }}`, `echo ${{ steps.setup-pdm.outputs.python-path }}`, and `echo ${{ steps.setup-pdm.outputs.python-version }}`. These values flow through YAML template substitution before the shell sees them, enabling script injection if any output contains shell metacharacters.

Locations:

- `.github/workflows/ci.yml:23`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings in .github/workflows/ci.yml: (1) Pinned actions/checkout@v3 to full SHA f43a0e5ff2bd294095638e18286ca9a3d1956744 with a # v3 comment. (2) Added top-level `permissions: contents: read` block — the workflow only needs to check out code. (3) Moved all four ${{ steps.setup-pdm.outputs.* }} expressions in the 'Check output' step into an env: block (PDM_BIN, PDM_VERSION, PYTHON_PATH, PYTHON_VERSION_OUT) and referenced them as double-quoted shell variables in the run: script, eliminating script injection risk.


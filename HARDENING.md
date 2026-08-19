<!-- markdownlint-disable -->

# Hardening Report: pdm-project--setup-pdm/v4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **pdm-project--setup-pdm/v4** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `actions/checkout@v3`, which is pinned to a mutable tag rather than an immutable 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling supply-chain attacks. It should be replaced with the full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v3`.

Locations:

- `.github/workflows/ci.yml:16`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/ci.yml` has no top-level `permissions:` key and the single job `Testing` also has no `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions (which may be `write-all`), granting broader access than necessary. A minimal `permissions:` block (e.g. `contents: read`) should be added.

Locations:

- `.github/workflows/ci.yml:1`

### script-injection (severity: high)

The 'Check output' step in `.github/workflows/ci.yml` directly interpolates `${{ steps.setup-pdm.outputs.pdm-bin }}`, `${{ steps.setup-pdm.outputs.pdm-version }}`, `${{ steps.setup-pdm.outputs.python-path }}`, and `${{ steps.setup-pdm.outputs.python-version }}` inside a `run:` shell command (sub-rule a). These `steps.*.outputs.*` expressions are substituted into the shell script before the shell parses it, so a malicious value containing shell metacharacters (`;`, `|`, `$(...)`, etc.) could execute arbitrary commands. Each value should be passed via an `env:` variable and then referenced as a double-quoted shell variable, e.g. `echo "$PDM_BIN"`.

Locations:

- `.github/workflows/ci.yml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings in hardened/action/.github/workflows/ci.yml: (1) Pinned actions/checkout@v3 to its full commit SHA a37ce9120846195fa4ece8f58b268e6043cb2f26 with a # v3 comment for readability. (2) Added a top-level `permissions: contents: read` block to enforce least-privilege. (3) Moved all four steps.setup-pdm.outputs.* expressions out of the run: shell script and into the step's env: block (PDM_BIN, PDM_VERSION, PYTHON_PATH, PYTHON_VERSION), then referenced them as double-quoted shell variables to prevent script injection.


<!-- markdownlint-disable -->

# Hardening Report: pdm-project--setup-pdm/v4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **pdm-project--setup-pdm/v4.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Check output' run: block directly interpolates ${{ steps.setup-pdm.outputs.pdm-bin }}, ${{ steps.setup-pdm.outputs.pdm-version }}, ${{ steps.setup-pdm.outputs.python-path }}, and ${{ steps.setup-pdm.outputs.python-version }} inside shell commands. Any ${{ ... }} expression interpolated directly into a run: script is a script-injection risk because the value is substituted by the YAML template engine before the shell ever sees it, allowing shell metacharacters to be injected. These should be moved to env: variables and referenced as quoted shell variables (e.g., "$VAR").

Locations:

- `.github/workflows/ci.yml:26`

### unpinned-uses (severity: high)

The step 'uses: actions/checkout@v3' references a mutable tag (@v3) rather than a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, making this a supply-chain risk. Pin to a specific SHA, e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v3.

Locations:

- `.github/workflows/ci.yml:18`

### missing-permissions (severity: medium)

The workflow file has no top-level 'permissions:' key and the 'Testing' job also has no job-level 'permissions:' key. Without explicit permissions, the workflow inherits the repository's default token permissions (which may be write-all), violating the principle of least privilege. Add a top-level 'permissions: {}' or restrict to only the scopes actually needed (e.g., 'contents: read').

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** missing-permissions, unpinned-uses, script-injection

**Notes:**

Fixed all three findings in .github/workflows/ci.yml: (1) Added top-level `permissions: {}` to enforce least privilege. (2) Pinned `actions/checkout@v3` to full SHA `a37ce9120846195fa4ece8f58b268e6043cb2f26 # v3`. (3) Moved all four `${{ steps.setup-pdm.outputs.* }}` expressions in the 'Check output' step into an `env:` block (PDM_BIN, PDM_VERSION, PYTHON_PATH, PYTHON_VERSION_OUT) and referenced them as quoted shell variables in the run script to eliminate script injection risk.


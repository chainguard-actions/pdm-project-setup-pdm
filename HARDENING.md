<!-- markdownlint-disable -->

# Hardening Report: pdm-project--setup-pdm/v4.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **pdm-project--setup-pdm/v4.6** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses actions/checkout@v3, which is pinned to a mutable tag rather than an immutable 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved. It should be pinned to a full SHA, e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v3.

Locations:

- `.github/workflows/ci.yml:16`

### script-injection (severity: high)

Sub-rule (a): The 'Check output' step directly interpolates GitHub Actions expressions (${{ steps.setup-pdm.outputs.pdm-bin }}, ${{ steps.setup-pdm.outputs.pdm-version }}, ${{ steps.setup-pdm.outputs.python-path }}, ${{ steps.setup-pdm.outputs.python-version }}) inside a run: shell command. These expressions are substituted into the shell script before execution, allowing any attacker-controlled value in those outputs to inject arbitrary shell commands. They should be passed via env: variables and referenced as quoted shell variables instead.

Locations:

- `.github/workflows/ci.yml:22`

### missing-permissions (severity: medium)

The workflow file has no top-level permissions: key and the single job (Testing) also has no job-level permissions: key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A minimal permissions block (e.g. contents: read) should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings in .github/workflows/ci.yml: (1) Pinned actions/checkout@v3 to full commit SHA a37ce9120846195fa4ece8f58b268e6043cb2f26 with # v3 comment for readability. (2) Moved all four ${{ steps.setup-pdm.outputs.* }} expressions from the 'Check output' run: shell script into an env: block, referencing them as quoted shell variables to prevent script injection. (3) Added top-level permissions: contents: read block to restrict the GITHUB_TOKEN to the minimum needed.


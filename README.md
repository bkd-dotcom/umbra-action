# Umbra Admission — GitHub Action

**Govern any coding agent's change to your repository, and attach a signed receipt.**

Every pull request — no matter which agent opened it (Claude Code, Codex, Cursor,
Copilot, Devin, or a human) — is run through the [umbra-core](https://github.com/bkd-dotcom/umbra-core)
admission pipeline:

```
executable contract  →  untrusted-text quarantine  →  required checks  →
independent verifier  →  earned authority (0/1/2)  →  Ed25519-signed receipt
```

The action posts the verdict as a PR comment, uploads the signed receipt as an
artifact, and **fails the check** unless the change earns the authority you
require. Make it a *required status check* and nothing merges without a receipt.
`auto_merge` is always false — Umbra governs the agent; a human merges.

## Usage

```yaml
name: Umbra Admission
on:
  pull_request:
permissions:
  contents: read
  pull-requests: write
jobs:
  admit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
        with:
          ref: ${{ github.event.pull_request.head.sha }}
          fetch-depth: 0                       # base must be reachable for the diff
      - uses: bkd-dotcom/umbra-action@v1
        with:
          min-authority: "1"                   # 0 observe · 1 analyze · 2 branch-PR
          signing-key: ${{ secrets.UMBRA_SIGNING_KEY }}   # optional: stable signed receipts
```

Add a `.umbra/admission.yaml` to your repo to declare the contract (allowed and
forbidden paths, diff budget, required checks). Without one, a conservative
default applies. See the [umbra-core docs](https://github.com/bkd-dotcom/umbra-core).

## Inputs

| Input | Default | Description |
|---|---|---|
| `mission` | review prompt | The bounded task the change claims to perform. |
| `min-authority` | `1` | Fail unless the change earns at least this level (0/1/2). |
| `agent` | `""` | Force `codex-cli` or `claude-code` to *re-run* the change. Blank governs the existing PR diff without invoking an agent. |
| `signing-key` | `""` | Base64 Ed25519 key (32+ bytes) for stable receipts. Falls back to a dev key (honestly flagged). |
| `umbra-version` | latest | Pin a specific `umbra-core` PyPI version. |
| `python-version` | `3.12` | Python to run on. |

## Outputs

| Output | Description |
|---|---|
| `authority-level` | The level the change earned (0/1/2). |
| `receipt-hash` | Canonical hash of the signed receipt. |

## How it works

This action is a thin wrapper over the `umbra-core` PyPI package. It stages the
PR's change as a working-tree diff, runs `umbra admit`, and enforces the earned
authority. The governance logic, contract, verifier, and receipts all live in
[umbra-core](https://github.com/bkd-dotcom/umbra-core).

## License

[MIT](LICENSE) © 2026 Binay Dalai.

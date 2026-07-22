# Contributing to Umbra Admission

This repo is a thin composite GitHub Action wrapping the
[umbra-core](https://github.com/bkd-dotcom/umbra-core) PyPI package. Most logic
lives in umbra-core; this repo is the `action.yml` glue.

## Where changes go

- **Governance logic** (contract, checks, verifier, receipts, trust boundary,
  authority) → contribute to [umbra-core](https://github.com/bkd-dotcom/umbra-core).
- **Action behavior** (inputs, staging the PR diff, sandbox setup, the PR comment,
  the enforcement gate) → this repo's [`action.yml`](action.yml).

## Guardrails for `action.yml`

- **Never interpolate `${{ inputs.* }}` (or `github.event.*`) into a `run:` bash
  body.** Pass values via `env:` and reference them as `$VAR`, and validate
  untrusted inputs (`agent`, `min-authority`) against fixed sets. This prevents
  script injection.
- **Fail closed**, not open — a step that can't reach the base commit or can't
  stage the diff must error, not silently pass an empty diff.
- Keep it a thin wrapper; pin `umbra-core>=` the latest hardened release.
- Validate YAML locally: `python -c "import yaml; yaml.safe_load(open('action.yml'))"`.

## Testing a change

Exercise it against the demo repo
([bkd-dotcom/umbra-demo-repo](https://github.com/bkd-dotcom/umbra-demo-repo)) or
any repo with a `.umbra/admission.yaml`: open a permitted PR (should pass) and a
forbidden-path PR (should fail with a verdict comment).

## Security

Do not open public issues for vulnerabilities — see [SECURITY.md](SECURITY.md).

## License

By contributing you agree your contributions are licensed under [MIT](LICENSE).

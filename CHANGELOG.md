# Changelog — Umbra Admission (GitHub Action)

Follows [Keep a Changelog](https://keepachangelog.com/) / [SemVer](https://semver.org/).
Pin `@v1` (moving) or an exact `@v0.1.3+` tag.

## [0.1.3] — 2026-07-22

### Security

- **Fixed a script-injection sink.** Action inputs (`mission`, `agent`,
  `min-authority`, `umbra-version`) are passed via `env:` and validated, never
  interpolated into a shell body.
- **Fail-closed PR staging.** The action errors if the base commit can't be
  fetched/reset, instead of silently admitting an empty diff.

### Added

- Installs **bubblewrap** on Linux and relaxes the unprivileged-userns clamp so
  required checks run **`sandboxed`** by default.
- `require-sandbox` input → `UMBRA_REQUIRE_SANDBOX` (fail closed on code-executing
  checks without a real sandbox).
- Defaults to installing the hardened `umbra-core>=0.1.3`.

## [0.1.0] — 2026-07-22

### Added

- Initial composite action: stages the PR diff, runs `umbra admit`, posts the
  verdict comment, uploads the signed receipt, and fails the check below the
  required authority.

> `v0.1.0`–`v0.1.2` (exact pins) are superseded — upgrade to `@v1`. See
> [SECURITY.md](SECURITY.md).

[0.1.3]: https://github.com/bkd-dotcom/umbra-action/releases/tag/v0.1.3
[0.1.0]: https://github.com/bkd-dotcom/umbra-action/releases/tag/v0.1.0

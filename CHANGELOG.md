# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]

### Added

- Optional layer C integration through the separate `legal-corpus` tool.
- First-run guidance using the separate tool's `python legal_corpus.py status`
  and `python legal_corpus.py setup` commands.
- Explicit fallback to online sources A/B when the local corpus is absent or setup is declined.

### Changed

- Draft version advanced to `0.2.0` for the optional corpus integration.
- Clarified that the skill must not assume a developer-specific filesystem path.

### Planned

- Publish the separate `legal-corpus` tool and this skill only after clean-machine verification.
- Add a stable launcher/discovery contract for invoking `legal-corpus` from any working directory.

## [0.1.1] - 2026-10-04

### Changed

- Removed the reference to the not-yet-released `judicial-act-verification` skill.
- Clarified that judicial-act verification is outside the scope of this repository.

## [0.1.0] - 2026-10-04

### Added

- Initial public release of `legal-analysis-rf-sources`.
- Official source workflow using `actual.pravo.gov.ru` as the primary source.
- Official `pravo.gov.ru/proxy/ips` fallback with response validation.
- Edition and source-text verification rules.
- MIT License.

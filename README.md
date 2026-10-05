# legal-analysis-rf-sources

An AI agent skill for retrieving and verifying Russian statutory sources.

Version: `0.2.0` (draft)
License: MIT
Copyright: Sergey Popov

## Purpose

This skill helps an agent locate, retrieve, and verify Russian legal source text. It covers:

- the official `actual.pravo.gov.ru` API;
- the official `pravo.gov.ru/proxy/ips` fallback;
- the boundary between source retrieval and legal reasoning.

It is intended to work with the related `legal_analysis` skill, which provides the broader method for facts, legal qualification, competing interpretations, and calibrated confidence.

## Scope of version 0.2.0

This release retrieves source text from the official online portals and can optionally use the separate `legal-corpus` tool as a local layer C. The corpus is not bundled into this repository.

Related tool: [legal-corpus](https://github.com/serenedos/legal-corpus) — the separate installer and manager for the optional local corpus.

## First-run workflow

The user-facing order is:

1. Install this skill.
2. On first use, the skill checks for `MANIFEST.json` in the standard user storage location. When the separate tool is available locally, the same check can be made with `python legal_corpus.py status`; a global `legal-corpus` launcher is not required in this release.
3. If no corpus is available, it gives the user the exact command `python legal_corpus.py setup` to run from the separate `legal-corpus` tool directory. This installs the default `min` profile in the user's standard application-data directory.
4. The user can later extend the corpus with `python legal_corpus.py setup --profile max` or fetch a selected document.

If the user does not install a local corpus, the skill continues to use online sources A and B. No developer-specific path is assumed.

## Corpus path configuration

The default corpus locations are:

```text
Windows: %LOCALAPPDATA%\legal-corpus
macOS:   ~/Library/Application Support/legal-corpus
Linux:   $XDG_DATA_HOME/legal-corpus or ~/.local/share/legal-corpus
```

For a custom location, an experienced user can set `LEGAL_CORPUS_HOME` or invoke the tool with `--root`. Automatic discovery of arbitrary custom locations is planned for a later version; it is intentionally documented as a manual configuration step for now.

## Repository layout

```text
legal-analysis-rf-sources/
├── SKILL.md
├── README.md
├── LICENSE
├── CHANGELOG.md
├── .gitignore
└── (no local corpus bundled; managed by the separate legal-corpus tool)
```

## Source priority

Use the following order for online retrieval:

1. `actual.pravo.gov.ru` — primary source and API.
2. `pravo.gov.ru/proxy/ips` — official fallback when the primary source is unavailable.
When switching from the primary online source to the fallback, record why.

## Limitations

- The online portals may be unavailable, slow, or return invalid responses.
- The Level A API uses HTTP and port `8000`.
- Level B responses require encoding and completeness validation.
- A source-retrieval skill does not determine the legal result of a dispute.
- Source text should be cited to the authoritative source where formal or official citation is required.
- A local corpus is optional and must be checked through its manifest before use.

## Related skills

- `legal_analysis` — legal reasoning and source-grounded analysis.


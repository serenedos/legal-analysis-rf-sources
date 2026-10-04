# legal-analysis-rf-sources

An AI agent skill for retrieving and verifying Russian statutory sources.

Version: `0.1.0`  
License: MIT  
Copyright: Sergey Popov

## Purpose

This skill helps an agent locate, retrieve, and verify Russian legal source text. It covers:

- the official `actual.pravo.gov.ru` API;
- the official `pravo.gov.ru/proxy/ips` fallback;
- the boundary between source retrieval and legal reasoning.

It is intended to work with the related `legal_analysis` skill, which provides the broader method for facts, legal qualification, competing interpretations, and calibrated confidence.

## Scope of version 0.1.0

This release retrieves source text from the official online portals. It does not install, update, or manage a local corpus. A selective local corpus and its management tool are planned for a later version.

## Repository layout

```text
legal-analysis-rf-sources/
├── SKILL.md
├── README.md
├── LICENSE
├── CHANGELOG.md
├── .gitignore
└── (no local corpus in version 0.1.0)
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

## Related skills

- `legal_analysis` — legal reasoning and source-grounded analysis.
- `judicial-act-verification` — verification of the existence and details of judicial acts.


---
name: legal-analysis-rf-sources
description: "Russian legal sources: official codex URLs and API."
version: 0.2.0
author: Sergey Popov
license: MIT
platform: [linux, macos, windows]
metadata:
  hermes:
    tags: [legal, sources, rf, russia]
    category: research
---

# When to Use

Use this skill when working with Russian statutory sources, including the codes of the Russian Federation, federal laws, and subordinate legislation: when an up-to-date rule text is needed, an instrument must be located, an edition must be checked, or a quotation must be verified.

This is a source-retrieval and provenance skill. It explains where and how to obtain source text. It does not replace the legal-reasoning discipline in `legal_analysis`.

# Optional Local Corpus

The skill can use the separate `legal-corpus` tool as an optional local layer C. The corpus is not bundled into this repository and the skill must not contain a developer-specific filesystem path.

Related tool: [legal-corpus](https://github.com/serenedos/legal-corpus). It is maintained as a separate repository and is installed independently.

On each task that may benefit from a local corpus:

1. Check for `MANIFEST.json` in the standard user location below. If the separate tool is available locally, its equivalent command is `python legal_corpus.py status`; this MVP does not require a globally installed launcher.
2. If the corpus is present, read its JSON status and use only the documents and current revisions reported by its `MANIFEST.json`.
3. If the corpus is absent, tell the user to run `python legal_corpus.py setup` from the separate `legal-corpus` tool directory, using the default `min` profile. Do not silently install `max` or download an unrequested body of law.
4. If setup is declined or unavailable, continue with the online A/B workflow described below.

The default user storage is selected by `legal-corpus`; do not infer it from the current working directory and do not scan the disk. Use this cross-platform contract:

```text
Windows: %LOCALAPPDATA%\legal-corpus
macOS:   ~/Library/Application Support/legal-corpus
Linux:   $XDG_DATA_HOME/legal-corpus or ~/.local/share/legal-corpus
```

`LEGAL_CORPUS_HOME` takes precedence over the platform default. An explicit `--root` takes precedence when the tool is invoked directly. Automatic discovery of an arbitrary custom directory is not part of this version; an experienced user may configure `LEGAL_CORPUS_HOME` or manually adjust the skill configuration until a later discovery mechanism is added. The light release does not silently scan the disk or install the corpus itself.

Layer C is a local fallback and provenance source, not proof that a text is current. When using it, report the corpus revision and checksum from the manifest and, where currentness matters, verify against source A or B.

# Source Architecture

The skill uses two authoritative online sources:

```text
A — actual.pravo.gov.ru       authoritative online source, primary
B — pravo.gov.ru/proxy/ips    official fallback, primary
```

The local corpus is optional. Without layer C, the skill works by retrieving sources from A or B.

# Source Selection

For an online source, use A first and B only when A is unavailable. An unavailable source means an HTTP error, timeout, empty response, or invalid response. If a fallback is used, state the reason for the switch.

# Level A — actual.pravo.gov.ru

The portal is a JavaScript application whose text is retrieved through an API. Retrieve the document text through the API rather than parsing the rendered HTML.

The API base is:

```text
http://actual.pravo.gov.ru:8000/api/ebpi/
```

The service uses HTTP and port `8000`. If the port is unavailable, the page may open while the document text remains unavailable.

Useful endpoints:

- `redactions/?bpa=ebpi&t={"hash":"<HASH>","ttl":3}` — list document editions, including `redid`, `reddate`, `redstatus`, and `hascontent`.
- `card/?bpa=ebpi&t={"hash":"<HASH>","ttl":3}` — document metadata such as `docname`, `docpassing`, and `docstate`.
- `redtext?bpa=ebpi&t=<REDID>&ttl=0` — text of an edition; the JSON response contains the document HTML in `redtext`.
- `attrsearch/?bpa=ebpi&q=<JSON>` — attribute search when the document URL is unknown.
- `getcontent/?bpa=ebpi&rdk=<REDID>` — document contents.

Determine the current edition from `redstatus: "актуальная"`, not from a hard-coded `dridx` or `redid`. A document hash is a stable document identifier; an edition identifier changes when the document changes.

When parsing `redtext`, preserve HTML structure. Paragraphs use `<p>`, headings use `<p class="H">`, and article subscript notation uses `<span class="W9">`. For example, `Статья 50<span class="W9">1</span>` means Article 50¹, not Article 501. Source notes such as `В редакции…` and `Утратил силу` are part of the official text and must be preserved.

Use current document metadata and the edition response to identify the applicable text. Do not treat a static list of edition identifiers as permanently current.

# Level B — pravo.gov.ru/proxy/ips

Use the official publication portal as the fallback when Level A is unavailable. It returns static HTML and may use `windows-1251` encoding. The service is slower and can return `502 Bad Gateway` or truncated responses.

Validate every response before using it. A valid page should contain `doc_itself=` and close with `</html>`. Otherwise, treat it as invalid and do not use it as source text.

The article text is in an iframe whose URL includes:

```text
?doc_itself=&nd=<ND>&page=1&rdk=<RDK>
```

Use the current `rdk` from the page's iframe link. Convert valid `windows-1251` content to UTF-8 before processing.

# Verification Rules

- Obtain and read the specific online source. Knowing an article number or an instrument title is not source verification.
- Existence and identifying details of a judicial act require separate verification. This repository does not provide a judicial-act verification skill.
- The source-retrieval decision does not itself establish the legal conclusion. Apply the source to the facts under `legal_analysis`.


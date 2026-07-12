---
type: main
id: 20260711-9f2a
title: spec-okf
description: Specs for Wiki
category: project
tags: [wiki, okf, meta]
timestamp: 2026-07-11T23:30:00Z
llm:
  authorship: 2
  review: reviewed
  human-only-lock: true
---

Root concept for this wiki. See the [index](/index.md) for what's here and the [log](/log.md) for update history. This note is the spec itself — structure and frontmatter conventions are defined below.

# Personal Wiki Spec

This wiki is a knowledge bundle conforming to the [Open Knowledge Format (OKF) v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md).

## Structure

The wiki is a directory tree of markdown files. Any `.md` file is a **concept** document, except two reserved filenames:

| Filename   | Purpose                                    |
|------------|---------------------------------------------|
| `index.md` | Optional directory listing (no frontmatter). |
| `log.md`   | Optional chronological update log.          |

A `.llm/` subfolder is also reserved: it holds LLM provenance sidecars (`type: llm-log`) for notes in the same folder. Files under `.llm/` are provenance records, not part of the browsable concept tree — see [LLM provenance](#llm-provenance) below.

## Concept documents

Every concept file MUST begin with a YAML frontmatter block containing one required field:

```yaml
---
type: <Type name>                  # REQUIRED
id: <Optional stable unique identifier, immutable across renames>
category: <Optional content classification>  # categories — see below
title: <Optional display name>
description: <Optional one-line summary>
resource: <Optional canonical URI, or map of named URIs, for the underlying asset(s)>
tags: [<tag>, <tag>, …]            # Optional
timestamp: <ISO 8601 datetime>     # Optional last-modified time
llm:                               # Optional LLM provenance + trust block — see below
  authorship: <1|2|3|4|5>
  review: <unreviewed|reviewed|verified>
  human-only-lock: <true|false>
sensitivity: <public|internal|confidential|restricted>  # required on person/dataset holding personal data
visibility: <listed|unlisted|hidden>
superseded_by: <path or id of the note that replaces this one>
see-also: [<path or id>, …]
---
```

**Required:** `type`.

**Recommended, in priority order:** `category`, `title`, `description`, `resource`, `tags`, `timestamp`, `llm`, `sensitivity`, `visibility`, `id`, `see-also`, `superseded_by`.

**Wiki extension — `category`:** an optional field, usable on any concept (not just folder roots), that classifies what kind of content the note is — e.g. `project`, `book`, `topic`, `article`. Open-ended, not centrally registered, same tolerance rules as `type`.

**Wiki extension — `llm`:** an optional map recording an LLM's relationship to the note, with three keys. `authorship` (1–5) records how the text was produced: `1` fully LLM-generated/autonomous, `2` LLM-generated/human-directed (feedback loop, no direct human edits), `3` LLM-drafted/human-edited, `4` human-drafted/LLM-edited, `5` fully human-authored. `review` (`unreviewed` | `reviewed` | `verified`) is the trust signal, kept separate from authorship: whether a human has read (`reviewed`) or fact-checked (`verified`) the note — absent means `unreviewed`, and it can be set even on fully human (`authorship: 5`) notes. `human-only-lock` (`true` | `false`) tells agents whether they may edit the note autonomously — `true` requires explicit human sign-off first. All keys are optional and tolerated missing, same as `category`. When `llm.authorship` is `1`–`4`, the note's detailed provenance (prompt, model) is also recorded — inline for `1`, in a `.llm/` sidecar for `2`–`4`. See [LLM field](/frontmatter/llm.md) for full guidance and [LLM provenance](#llm-provenance) below.

**Wiki extension — `sensitivity`:** classifies how sensitive a note's content is, on a controlled scale: `public` (nothing sensitive, freely shareable), `internal` (not for public distribution, but nothing personal), `confidential` (contains personal data / PII or other sensitive information — the minimum for any note holding personal data), `restricted` (special-category or high-risk data: health, financial, biometric, credentials, government IDs, or otherwise legally protected). It is **required** on `type: person` notes and on `type: dataset` notes that contain personal information (minimum `confidential`), and optional elsewhere. Agents must not surface or transmit `restricted` notes without explicit human authorization. See [Sensitivity](/frontmatter/sensitivity.md).

**Wiki extension — `visibility`:** controls how a note is surfaced within the wiki: `listed` (default — appears in `index.md` and search, agents may surface it), `unlisted` (readable if linked, but excluded from index generation and routine search), `hidden` (agents should not surface or expose it unless the human asks for it by name). Orthogonal to `sensitivity`: sensitivity is about the content, visibility about exposure. See [Visibility](/frontmatter/visibility.md).

**Wiki extension — `id`:** an optional stable, unique-within-bundle identifier, assigned once and never changed even when the file is renamed or moved. It decouples a note's identity from its path, so `see-also` and `superseded_by` references survive renames. Recommended form is a short opaque slug (e.g. a date-prefixed base36 string or a ULID), not derived from the title. See [Id](/frontmatter/id.md).

**Wiki extension — `superseded_by`:** marks a note as replaced by a newer one, pointing to the successor (bundle-relative path preferred, or an `id`). Presence flags the note as historical; consumers and agents should prefer the successor. Distinct from a `type: redirect` note (a thin pointer with no content of its own) — a superseded note keeps its content for history. See [Superseded by](/frontmatter/superseded_by.md).

**Wiki extension — `see-also`:** a list of related concepts as bundle-relative paths (or `id`s) — a machine-readable, frontmatter-level relation complementing inline body links, so agents can traverse relationships without parsing prose. See [See also](/frontmatter/see-also.md).

**Wiki extension — `resource`:** may be either a single URI string (the common case) or a YAML map of named URIs when a concept has more than one canonical link — e.g. a `type: person` note with several contact points and profiles, or an `article`-category note indexed under several venues (DOI, PMID, journal, proceedings, preprint):

```yaml
resource:
  email: mailto:jane@example.com
  orcid: https://orcid.org/0000-0000-0000-0000
  linkedin: https://linkedin.com/in/jane
  bluesky: https://bsky.app/profile/jane.bsky.social
  phone: tel:+1-555-0100
```

Map keys are open-ended, not centrally registered — same tolerance rules as `category` and `tags`. See [Resource](/frontmatter/resource.md) for full guidance.

## Folder root concepts (`type: main`)

A folder MAY have one concept file whose `type` is `main`, marking it as the root/summary concept for that folder. The file is named after the folder itself, not `index.md`:

Not every folder needs one, and nested subfolders don't inherit a requirement for their own `type: main` just because a parent or sibling has one. Whether a given folder — or which folder within a related group of subfolders — gets a `main` note is an editorial call for the wiki owner, made when that folder represents a coherent enough unit to deserve a root/summary concept. A cluster of subfolders can share a single ancestor's `main` note as their effective root without each one having its own.

```
projects/
└── spec-okf/
    ├── spec-okf.md      # type: main — root concept for this folder
    ├── index.md
    ├── log.md
    └── ...other concepts
```

**Wiki extension rule:** any folder containing a `type: main` file MUST also have an `index.md` and a `log.md` in that same folder. (This is stricter than base OKF, which treats both files as always-optional.)

## Index files

An `index.md` MAY appear in any directory, including the wiki root, to list that directory's contents. It has **no frontmatter** — just a body grouping links under headings:

```markdown
# Section / Group Heading

* [Title 1](relative-url-1) - short description of item 1
* [Title 2](relative-url-2) - short description of item 2

# Another Section

* [Subdirectory](subdir/) - short description of the subdirectory
```

Descriptions should be pulled from the linked concept's `description` field when available. `index.md` can be generated by hand or on the fly; consumers may synthesize one if it's missing.

## Log files

A `log.md` MAY appear at any level to record that scope's update history, as a flat list of date-grouped entries, newest first:

```markdown
# Directory Update Log

## 2026-07-11
* **Update**: Added new entry for [Concept Title](/path/to/concept.md).
* **Creation**: Established this directory.

## 2026-07-01
* **Initialization**: Created foundational structure.
```

Date headings MUST use ISO 8601 `YYYY-MM-DD`. The leading bold word (`**Update**`, `**Creation**`, `**Deprecation**`, etc.) is a convention, not a requirement.

## LLM provenance

Notes with LLM involvement in their text (`llm.authorship` `1`–`4`) carry a durable record of *how* that text was produced — the prompt, model, and resulting authorship level. For `llm.authorship: 1` this is a single inline block at the top of the note body; for `2`–`4` it moves to a sidecar concept with `type: llm-log`, stored in a reserved `.llm/` folder beside the note and named `<note>-llm.md`.

`.llm/` is a reserved system folder: it is never listed in `index.md`, the `type: main` → `index.md`+`log.md` rule never applies to it, reserved-filename semantics are suspended inside it, and reserved files (`index.md`, `log.md`) get no sidecar. Entries are reverse-chronological with full ISO 8601 timestamps. This provenance is deliberately distinct from `log.md` — which records *what* changed, not *how* it was produced — and is a recommended convention, not a conformance requirement.

See [LLM provenance](/llm.md) for the entry format, the `1`→`2` migration, and full guidance.

## Conformance

This wiki is OKF v0.1 conformant if:

1. Every concept `.md` file has a parseable YAML frontmatter block.
2. Every frontmatter block has a non-empty `type` field.
3. `index.md` and `log.md`, where present, follow the structure in OKF §6 and §7.
4. *(Wiki extension)* Every folder containing a `type: main` file has both an `index.md` and a `log.md`.
5. *(Wiki extension)* Every `type: person` note, and every `type: dataset` note that contains personal information, has a `sensitivity` field.

Missing optional fields, unknown `type`/`category` values, unknown `sensitivity`/`visibility` values, out-of-range `llm.authorship` values, missing or partial LLM provenance (inline blocks or `.llm/` sidecars), and broken links or `see-also`/`superseded_by`/`id` references are all tolerated — they do not break conformance. LLM provenance is a recommended convention, not a conformance requirement; the `sensitivity` rule (5) is the only one of the new fields that affects conformance, and only for `person`/`dataset` notes.

## Reference

Full specification: https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md

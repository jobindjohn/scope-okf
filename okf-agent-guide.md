# Agent Instructions — spec-okf Wiki

This wiki is an [OKF v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md) knowledge bundle. Full rules: [spec-okf.md](/spec-okf.md). Follow the essentials below when reading or editing notes.

## What files are
- Any `.md` is a **concept** doc **except** `index.md` (directory listing, no frontmatter) and `log.md` (update log).
- `.llm/` is a reserved folder for provenance sidecars — never list it, never apply normal file rules inside it.

## Every concept needs frontmatter
Only `type` is required. Add fields in this priority order when known:
`category`, `title`, `description`, `resource`, `tags`, `timestamp`, `llm`, `sensitivity`, `visibility`, `id`, `aliases`, `see-also`, `superseded_by`.

```yaml
---
type: <required>
category: <project|book|topic|article|…>
title: <display name>
description: <one line>
timestamp: <ISO 8601>
---
```

## Before editing a note — check the `llm` block
- `human-only-lock: true` → **do not edit autonomously.** Get explicit human sign-off first.
- `authorship` 1–5: `1` fully LLM, `2` LLM/human-directed, `3` LLM-drafted/human-edited, `4` human-drafted/LLM-edited, `5` fully human. Set it honestly for text you produce.
- `review` (`unreviewed`|`reviewed`|`verified`) is a separate trust signal — don't bump it; only a human marks reviewed/verified.
- Record provenance for authorship `1`–`4`: inline block at top of body for `1`; a `.llm/<note>-llm.md` sidecar (`type: llm-log`) for `2`–`4`.

## Sensitivity (this one affects conformance)
- **Required** on every `type: person` note and any `type: dataset` with personal data — minimum `confidential`.
- Scale: `public` → `internal` → `confidential` (PII) → `restricted` (health, financial, biometric, credentials, IDs).
- **Never surface or transmit `restricted` notes** without explicit human authorization. Respect `visibility: hidden`/`unlisted` too.

## Folder roots
- A folder's root concept is `type: main`, named after the folder (not `index.md`).
- **Any folder with a `type: main` file MUST also have `index.md` and `log.md`.** Create/maintain both.

## Linking
- Path links `[text](/path.md)` are the portable default; wikilinks `[[name]]` also work.
- Both resolve by: `id` → filename → `title`/`aliases`, first match wins. Broken links are tolerated.

## Keep these current when you change things
- Add a dated entry to the folder's `log.md` (newest first, `## YYYY-MM-DD`) describing *what* changed.
- Update `index.md` entries; pull descriptions from each note's `description` field.

## Conformance musts
1. Parseable YAML frontmatter on every concept.
2. Non-empty `type` on every block.
3. Valid `index.md` / `log.md` structure where present.
4. `type: main` folders have `index.md` + `log.md`.
5. `person` / personal-data `dataset` notes have `sensitivity`.

Everything else (unknown values, missing optional fields, broken refs) is tolerated — don't break these five.

---
type: llm-log
title: LLM log — scope-okf
timestamp: 2026-07-12T00:00:00Z
---

Provenance for [scope-okf.md](/scope-okf.md). Trail starts at the first LLM interaction recorded under this convention; earlier history predates it and is not backfilled.

## 2026-07-12T00:10Z · claude-opus-4-8 · → authorship 2
**Prompt:** "Right now linking is mentioned in the spec-okf.md. Better to have another file called linking.md"
**Change:** Extracted the `## Linking` section into a standalone [linking.md](/linking.md), replacing it in scope-okf.md with a three-paragraph summary plus a link (same pattern as the earlier llm.md extraction). The `aliases` frontmatter definition stayed in scope-okf.md.

## 2026-07-12T00:00Z · claude-opus-4-8 · → authorship 2
**Prompt:** A linking brainstorm followed by decisions — resolver precedence `id` → basename → `title`/`aliases`; shortest-unique-path disambiguation; add `aliases`, display-text and `#heading` forms, embeds; unify body `[[links]]`, `see-also`, `superseded_by` under one resolver; tolerant conformance. Then three settled choices: "add - for space" (fold `-`/space/`_`), heading match = Obsidian exact-text, tie-break = same folder first then traverse up and siblings progressively. "Prepare the changes and edit the spec-okf file."
**Change:** Added a `## Linking` section (two link spellings, the unified resolver with normalization and reserved-name exclusion, wikilink syntax, shortest-unique-path + deterministic tie-break, body-vs-frontmatter syntax split, hardening, gotchas, tolerant conformance stance); added the `aliases` frontmatter field (example, priority list, wiki-extension paragraph); bumped `timestamp` and reverted `review` to `unreviewed` pending human re-read.

## 2026-07-11T23:30Z · claude-opus-4-8 · → authorship 2
**Prompt:** "rework authorship into llm. Add new fields for review, visibility, id, superseded_by, see-also, / sensitivity: come up with keywords (required for notes with type:person or type:dataset with personal info) / llm: authorship 1-5, review: unreviewed/reviewed/verified, human-only-lock: true/false"
**Change:** Replaced the standalone `authorship` scalar with an `llm` map (`authorship`, `review`, `human-only-lock`); added `sensitivity` (with a controlled vocabulary and a new conformance rule for `person`/`dataset` notes), `visibility`, `id`, `superseded_by`, and `see-also` as wiki extensions; updated the frontmatter example, recommended order, LLM-provenance section, and conformance list; set the spec's own `human-only-lock: true`.

## 2026-07-11T22:00Z · claude-opus-4-8 · → authorship 2
**Prompt:** "Move the LLM related notes into an llm.md folder to keep the spec-okf.md concise. start an .llm/ folder in the process also to serve as an illustration"
**Change:** Extracted the detailed LLM provenance section into a new [llm.md](/llm.md), leaving a two-paragraph summary plus a link in scope-okf.md.

## 2026-07-11T21:30Z · claude-opus-4-8 · → authorship 2
**Prompt:** Decisions from the brainstorm, ending "Keep the -llm suffix / also exempt reserved files + suspend reserved semantics inside .llm/. Write to update spec-okf.md" — i.e. add a durable LLM provenance record: type:llm-log sidecar, authorship 1 inline with 1→2 migration, .llm/ as a reserved system folder, anti-drift charter vs log.md, reverse-chronological entries.
**Change:** Added the LLM provenance section; cross-referenced it from the Structure table, the `authorship` field paragraph, and Conformance; bumped `timestamp`.

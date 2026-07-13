# Directory Update Log

## 2026-07-13

* **Update**: Marked [scope-okf](/scope-okf.md) `review: reviewed` and repointed its end-of-document "Full specification" reference to the project repo (`https://github.com/jobindjohn/scope-okf`), keeping OKF v0.1 as the credited base format. Human-directed edit to a `human-only-lock: true` note; recorded in `.llm/scope-okf-llm.md`.

* **Update**: Set [scope-okf-SPEC](/scope-okf-SPEC.md)'s `resource` to the project repo (`https://github.com/jobindjohn/scope-okf`). After human review, bumped `llm.authorship` 1→2 and `review`→`reviewed`, and migrated provenance from the note's inline block into a new `.llm/scope-okf-SPEC-llm.md` sidecar.

* **Creation**: Added [scope-okf-SPEC](/scope-okf-SPEC.md) — a comprehensive, self-contained single-file specification for LLMs, consolidating [scope-okf](/scope-okf.md), [linking](/linking.md), [llm](/llm.md), and every `frontmatter/*.md` field doc into one file (`authorship: 1`, `unreviewed`). Because the chosen name case-collides with the existing one-page `scope-okf-spec.md` on a case-insensitive filesystem, the sync layer renamed the old file to `scope-okf-spec-.md`; updated the index links to match.

* **Rename**: Renamed `okf-agent-guide.md` → [scope-okf-spec](/scope-okf-spec.md); updated the link and text in README.md accordingly.

* **Rename**: Renamed the wiki/project from `spec-okf` to `scope-okf`. Root concept `spec-okf.md` → [scope-okf](/scope-okf.md) (`id` unchanged: `20260711-9f2a`); sidecar `.llm/spec-okf-llm.md` → `.llm/scope-okf-llm.md`. Updated all internal links and mentions across README.md, okf-agent-guide.md, linking.md, llm.md, every `frontmatter/*.md` field doc, and this log's historical entries.

## 2026-07-12

* **Refactor**: Extracted the **Linking** section from [scope-okf](/scope-okf.md) into a standalone note [Linking](/linking.md) (with a `.llm/` provenance sidecar), leaving a three-paragraph summary plus a link in the spec — mirroring the earlier [llm.md](/llm.md) extraction. The `aliases` field definition stays in scope-okf.md. Added Linking to the index.
* **Update**: Added a **Linking** section to [scope-okf](/scope-okf.md) introducing Obsidian-style wikilinks (`[[name]]`) as a tolerant extension over the existing path-based links. Defines one resolver used everywhere — `id` → filename basename → `title`/`aliases`, case-folded with `-`/space/`_` equivalent — plus shortest-unique-path disambiguation with a same-folder-first-then-widening tie-break, `[[name|display]]`/`[[name#Heading]]`/`![[embed]]` forms, a body-`[[ ]]`-vs-frontmatter-bare-strings split, hardening to portable markdown, and an unchanged (tolerant) conformance stance. scope-okf revised under human sign-off (it is `human-only-lock: true`); `review` reverted to `unreviewed` pending re-read.
* **Creation**: Added the `aliases` frontmatter field (optional alternative names, matched at the lowest resolver tier so they never shadow a filename) with a field doc [Aliases](/frontmatter/aliases.md); added it to the frontmatter example and recommended-order list in [scope-okf](/scope-okf.md).

## 2026-07-11

* **Update**: Reworked the standalone `authorship` scalar into an `llm` map in [scope-okf](/scope-okf.md) — `llm.authorship` (1–5) now sits alongside a `review` trust signal (`unreviewed`/`reviewed`/`verified`) and a `human-only-lock` flag governing autonomous agent edits; renamed the field doc `authorship.md` → [LLM](/frontmatter/llm.md) and migrated every existing note to the new form (all LLM-authored `review: reviewed`). scope-okf itself set `human-only-lock: true`.
* **Creation**: Added five frontmatter fields, each with a doc: [Sensitivity](/frontmatter/sensitivity.md) (controlled `public`/`internal`/`confidential`/`restricted` vocabulary; **now required** on `type: person` and personal-data `type: dataset` notes — new conformance rule 5), [Visibility](/frontmatter/visibility.md) (`listed`/`unlisted`/`hidden` surfacing control), [Id](/frontmatter/id.md) (stable identifier surviving renames), [Superseded by](/frontmatter/superseded_by.md) (replacement pointer), and [See also](/frontmatter/see-also.md) (machine-readable related-notes relation); added a `dataset` candidate to [Type](/frontmatter/type.md).
* **Creation**: Moved the LLM provenance detail out of [scope-okf](/scope-okf.md) into a new [LLM provenance](/llm.md) note (spec now keeps a two-paragraph summary + link); started the reserved `.llm/` folder as a live illustration, with `type: llm-log` sidecars for the two `authorship: 2` notes ([scope-okf-llm](/.llm/scope-okf-llm.md), [llm-llm](/.llm/llm-llm.md)); added llm.md to the index.
* **Update**: Added an **LLM provenance** section to [scope-okf](/scope-okf.md): notes with `authorship` `1`–`4` record how their text was produced (prompt, model, resulting level) — inline for `1`, in a `type: llm-log` sidecar under a reserved `.llm/` folder (named `<note>-llm.md`) for `2`–`4`, with a defined `1`→`2` migration and a reverse-chronological entry format. Charter added to keep it distinct from `log.md`; provenance is recommended, not required for conformance.
* **Update**: Expanded `authorship` from a 1–4 to a 1–5 scale in [scope-okf](/scope-okf.md), inserting a new level 2 ("LLM-generated, human-directed" — feedback loop, no direct human edits) between autonomous LLM generation and human-edited text; revised [Authorship](/frontmatter/authorship.md) accordingly and migrated all 8 existing `authorship: 1` notes in this wiki to `2`, since none were single-shot with zero feedback.
* **Update**: Clarified in [scope-okf](/scope-okf.md) and [Type](/frontmatter/type.md) that `type: main` is never required per-folder — it's an editorial choice by the wiki owner which folder (if any) in a related group of subfolders serves as the root.
* **Update**: Added candidate `type` values (person, reference, draft/stub, template, redirect) to [Type](/frontmatter/type.md), split into "in use" vs. "candidates" tables.
* **Creation**: Added [Type](/frontmatter/type.md), documenting the required `type` field; updated index.
* **Creation**: Added [Title](/frontmatter/title.md) and [Description](/frontmatter/description.md), documenting those fields; updated index.
* **Update**: Extended `resource`'s map form to cover `article`-category notes with multiple venues (doi, pmid, journal, proceedings, preprint), not just `type: person`; revised [Resource](/frontmatter/resource.md) guidance and key vocabulary accordingly.
* **Update**: Generalized `resource` in [scope-okf](/scope-okf.md) to allow a map of named URIs (email, phone, orcid, linkedin, bluesky, …), for `type: person` notes and similar; updated [Resource](/frontmatter/resource.md) with the map form.
* **Creation**: Added [Resource](/frontmatter/resource.md), documenting the `resource` field; updated index.
* **Creation**: Added [Tags](/frontmatter/tags.md), documenting the `tags` field; updated index.
* **Update**: Added `authorship` (1–4 scale) to the frontmatter spec in [scope-okf](/scope-okf.md); added [Authorship](/frontmatter/authorship.md) doc and updated index.
* **Update**: Moved [Categories](/frontmatter/category.md) into a new `frontmatter/` folder; updated index links accordingly.
* **Creation**: Added [Categories](/frontmatter/category.md), documenting the `category` field.
* **Creation**: Established the wiki root — added [scope-okf](/scope-okf.md), index, and log.

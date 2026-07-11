# Directory Update Log

## 2026-07-11

* **Update**: Added an **LLM provenance** section to [spec-okf](/spec-okf.md): notes with `authorship` `1`–`4` record how their text was produced (prompt, model, resulting level) — inline for `1`, in a `type: llm-log` sidecar under a reserved `.llm/` folder (named `<note>-llm.md`) for `2`–`4`, with a defined `1`→`2` migration and a reverse-chronological entry format. Charter added to keep it distinct from `log.md`; provenance is recommended, not required for conformance.
* **Update**: Expanded `authorship` from a 1–4 to a 1–5 scale in [spec-okf](/spec-okf.md), inserting a new level 2 ("LLM-generated, human-directed" — feedback loop, no direct human edits) between autonomous LLM generation and human-edited text; revised [Authorship](/frontmatter/authorship.md) accordingly and migrated all 8 existing `authorship: 1` notes in this wiki to `2`, since none were single-shot with zero feedback.
* **Update**: Clarified in [spec-okf](/spec-okf.md) and [Type](/frontmatter/type.md) that `type: main` is never required per-folder — it's an editorial choice by the wiki owner which folder (if any) in a related group of subfolders serves as the root.
* **Update**: Added candidate `type` values (person, reference, draft/stub, template, redirect) to [Type](/frontmatter/type.md), split into "in use" vs. "candidates" tables.
* **Creation**: Added [Type](/frontmatter/type.md), documenting the required `type` field; updated index.
* **Creation**: Added [Title](/frontmatter/title.md) and [Description](/frontmatter/description.md), documenting those fields; updated index.
* **Update**: Extended `resource`'s map form to cover `article`-category notes with multiple venues (doi, pmid, journal, proceedings, preprint), not just `type: person`; revised [Resource](/frontmatter/resource.md) guidance and key vocabulary accordingly.
* **Update**: Generalized `resource` in [spec-okf](/spec-okf.md) to allow a map of named URIs (email, phone, orcid, linkedin, bluesky, …), for `type: person` notes and similar; updated [Resource](/frontmatter/resource.md) with the map form.
* **Creation**: Added [Resource](/frontmatter/resource.md), documenting the `resource` field; updated index.
* **Creation**: Added [Tags](/frontmatter/tags.md), documenting the `tags` field; updated index.
* **Update**: Added `authorship` (1–4 scale) to the frontmatter spec in [spec-okf](/spec-okf.md); added [Authorship](/frontmatter/authorship.md) doc and updated index.
* **Update**: Moved [Categories](/frontmatter/category.md) into a new `frontmatter/` folder; updated index links accordingly.
* **Creation**: Added [Categories](/frontmatter/category.md), documenting the `category` field.
* **Creation**: Established the wiki root — added [spec-okf](/spec-okf.md), index, and log.

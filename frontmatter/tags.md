---
type: note
category: meta
title: Tags
description: How the tags field is used for free-form keywords and cross-cutting association in this wiki.
tags: [okf, meta, tags]
timestamp: 2026-07-11T18:00:00Z
authorship: 1
---

Notes on the `tags` field, expanding on its definition in [spec-okf.md](/spec-okf.md).

# What `tags` is for

`tags` is a list of free-form keywords attached to a concept, used for search and cross-cutting association — it answers "what is this note *about*?" A note can carry several tags at once, unlike `category` or `type`, which are normally singular.

# tags vs. category vs. type

* `type` is the required, structural field — what role the file plays in the bundle (e.g. `main` for a folder root).
* `category` is a single value classifying what *kind* of thing the note is (`project`, `book`, `topic`, `article`, …).
* `tags` are a list of keywords describing subject matter, themes, or associations — a note can belong to one `category` but carry many `tags`.

# Conventions

* `tags` is optional, like `category` and `authorship` — missing or empty values don't break conformance (per spec-okf.md's conformance rules).
* Written as a YAML flow list: `tags: [wiki, okf, meta]`.
* Keep tags lowercase and hyphenated for multi-word concepts (e.g. `knowledge-catalog`, not `Knowledge Catalog`), so they stay greppable and consistent.
* Tags are open-ended and not centrally registered — there's no fixed vocabulary to conform to, unlike the `category` table in [Categories](/frontmatter/category.md).
* Reuse existing tags over inventing near-duplicates (`llm` vs `llms` vs `ai`) — check other notes in the same folder or topic before adding a new one.

# Guidance

* Favor a handful of specific tags over many generic ones — 2–4 tags that narrow down the note are more useful than a long list of broad terms.
* Don't tag with the same word already carried by `type` or `category` — it's redundant (e.g. a `type: main` note doesn't also need a `main` tag).
* Tags describe the note's content, not its provenance or workflow state — use `authorship` for provenance, not a tag like `llm-generated`.

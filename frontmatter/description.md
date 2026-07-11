---
type: note
category: meta
title: Description
description: How the description field records a one-line summary of a concept.
tags: [okf, meta, description]
timestamp: 2026-07-11T18:00:00Z
authorship: 1
---

Notes on the `description` field, expanding on its definition in [spec-okf.md](/spec-okf.md).

# What `description` is for

`description` is a one-line summary of what a concept covers. It's what `index.md` entries are pulled from (per spec-okf.md's index-file convention) and what a reader should see to decide whether to open the note.

# description vs. other fields

* `title` is a short label; `description` is a sentence that explains it — `title: Tags` next to `description: How the tags field is used for free-form keywords and cross-cutting association in this wiki.`
* `tags` are keywords for search; `description` is prose for a human skimming an index.
* The note body can (and usually should) restate or expand on `description` in its opening line, but `description` itself must stand alone without reading the body.

# Conventions

* `description` is optional, like `category` and `tags` — missing values don't break conformance (per spec-okf.md's conformance rules).
* One sentence, no trailing period required either way — pick one style and stay consistent within a folder (this wiki omits periods).
* Write it as plain text, not a markdown link or list.
* Should be meaningful out of context — avoid descriptions like "Notes on this topic" that could apply to any note.

# Guidance

* Set `description` on every note where `index.md` might link to it — it's the main payoff, since index entries pull from this field.
* Keep it factual and specific rather than promotional; it's a summary, not a pitch.
* If a note's scope changes significantly, update `description` in the same edit — a stale description is worse than a missing one.

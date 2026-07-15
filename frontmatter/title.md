---
type: note
category: meta
title: Title
description: How the title field records a concept's display name.
tags: [okf, meta, title]
timestamp: 2026-07-11T23:30:00Z
llm-authorship: 2
llm-review: reviewed
---

Notes on the `title` field, expanding on its definition in [scope-okf.md](/scope-okf.md).

# What `title` is for

`title` is the human-readable display name for a concept — what a reader or index should show instead of the filename. It answers "what is this note called?"

# title vs. other fields

* The filename is the stable, path-safe identifier; `title` is the readable label — they can diverge (e.g. `scope-okf.md` has `title: scope-okf`, but a file like `frontmatter/tags.md` has `title: Tags`, not `tags`).
* `description` expands on `title` with a one-line summary; `title` alone should be short enough to use as link text.
* For a `type: main` note, `title` conventionally matches the folder name (in natural casing), since the file itself is already named after the folder.

# Conventions

* `title` is optional, like `category` and `tags` — missing values don't break conformance (per scope-okf.md's conformance rules).
* Use normal title casing, not the filename's casing or separators: `title: Authorship`, not `title: authorship` or `title: authorship-notes`.
* Keep it short — a few words, not a sentence. If it needs a clause to make sense, that belongs in `description` instead.
* When missing, consumers may fall back to a formatted version of the filename — don't rely on this if the filename is non-obvious (dates, slugs, abbreviations).

# Guidance

* Set `title` whenever the filename isn't already a good display name — most notes benefit from it.
* Don't restate `category` or `type` inside `title` (e.g. avoid `title: Tags (meta)`) — those are separate fields for a reason.
* If a note gets renamed or repurposed, update `title` to match rather than leaving it describing the old content.

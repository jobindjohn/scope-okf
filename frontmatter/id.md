---
type: note
id: 20260711-1d4c
category: meta
title: Id
description: How the id field gives a note a stable identifier that survives renames and moves.
tags: [okf, meta, id]
timestamp: 2026-07-11T23:30:00Z
llm-authorship: 2
llm-review: reviewed
see-also: [/frontmatter/see-also.md, /frontmatter/superseded_by.md, /frontmatter/title.md]
---

Notes on the `id` field, expanding on its definition in [scope-okf.md](/scope-okf.md).

# What `id` is for

`id` is a stable, unique-within-bundle identifier for a note, assigned once and never changed — even when the file is renamed or moved. It decouples a note's *identity* from its *path*.

The wiki normally uses the filename as identity, which is why renaming a note risks breaking links and needs a `type: redirect`. An `id` sidesteps that: references made by `id` keep pointing at the right note no matter where the file goes. It matters more now that agents rename and reorganize files — an `id` is what lets `see-also` and `superseded_by` references survive that churn.

# Form

* A short, opaque slug — e.g. a date-prefixed base36 string (`20260711-9f2a`) or a ULID. The notes in this wiki use a `YYYYMMDD-xxxx` form.
* **Not** derived from the title or filename — if it were, a rename would invalidate it, defeating the purpose.
* Unique within the bundle. Assigned at creation and then immutable.

# id vs. title vs. filename

* The filename is a human-facing, path-safe label that can change.
* [`title`](/frontmatter/title.md) is the human-readable display name that can change.
* `id` is the machine-facing identity that must *not* change — the one handle guaranteed stable across a note's life.

# Conventions

* `id` is optional — a note without one is still conformant, and is simply identified by its path (per scope-okf.md's conformance rules).
* Once assigned, treat `id` as write-once. If you must correct a typo'd id, treat it like a rename: update every reference.
* Backfilling ids onto existing notes is fine but not required — add them lazily, starting with notes that are linked to or likely to move.

# Guidance

* Set `id` on notes you expect to be renamed, moved, merged, or referenced from `see-also`/`superseded_by` — anywhere path-stability matters.
* (For LLMs) When you move or rename a note that has an `id`, keep the `id` unchanged — that's the whole point. Never mint a new `id` for the same concept.
* Don't reuse an `id` from a deleted note for a different concept; retire ids rather than recycling them.

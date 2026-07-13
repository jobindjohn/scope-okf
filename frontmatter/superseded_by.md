---
type: note
id: 20260711-sup7
category: meta
title: Superseded by
description: How the superseded_by field marks a note as replaced by a newer one and points to its successor.
tags: [okf, meta, superseded, deprecation]
timestamp: 2026-07-11T23:30:00Z
llm:
  authorship: 2
  review: reviewed
see-also: [/frontmatter/id.md, /frontmatter/type.md, /frontmatter/see-also.md]
---

Notes on the `superseded_by` field, expanding on its definition in [scope-okf.md](/scope-okf.md).

# What `superseded_by` is for

`superseded_by` marks a note as replaced by a newer one and points to its successor. It answers "is this still current, and if not, what replaces it?" Presence of the field flags the note as historical — kept for the record, but no longer the place to look.

As agents add and revise notes at speed, superseded content accumulates. Without a marker, a reader or agent can't tell a live note from one that a newer note has quietly overtaken. `superseded_by` makes that explicit and machine-readable.

# Form

* A bundle-relative path to the successor note (`superseded_by: /frontmatter/llm.md`), preferred; or an [`id`](/frontmatter/id.md) where path-stability matters (`superseded_by: 20260711-ll0m`).
* Points at exactly one successor. If a note was split into several, point at the most central one and use `see-also` for the rest.

# superseded_by vs. a redirect

* `superseded_by` stays on a note that keeps its own content — the text is preserved for history, just flagged as overtaken.
* A `type: redirect` note has no content of its own; its only job is to forward from an old path to a new one.

Use `superseded_by` when the old note is worth keeping and reading; use `redirect` when the old path just needs to not dead-end.

# Conventions

* `superseded_by` is optional; a dangling target (a path or id that doesn't resolve) is tolerated (per scope-okf.md's conformance rules), though it should be fixed.
* Set it in the same edit that creates or designates the successor, and bump `timestamp`.
* Consider also lowering the old note's [`visibility`](/frontmatter/visibility.md) to `unlisted` so the superseded note stops surfacing in indexes and search.
* Record the supersession in the folder's `log.md` (as a `**Deprecation**` entry) so the change is visible in history.

# Guidance

* (For LLMs) When a note carries `superseded_by`, prefer its successor for answers, and don't edit the superseded note's content except to fix the pointer — its job now is to be an accurate historical record.
* Don't delete a superseded note by default — the pointer plus preserved content is more useful than a gap. Delete only when the old content is actively misleading.

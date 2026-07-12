---
type: note
category: meta
title: Type
description: How the type field marks a concept's structural role in the wiki — the one required frontmatter field.
tags: [okf, meta, type]
timestamp: 2026-07-11T23:30:00Z
llm:
  authorship: 2
  review: reviewed
---

Notes on the `type` field, expanding on its definition in [spec-okf.md](/spec-okf.md).

# What `type` is for

`type` is the base OKF field, and the only one that's required — every concept file must have a non-empty `type`. It marks what structural role the file plays in the bundle, not what the note is about.

A `type` value is only worth introducing if it changes how a note is *handled* — parsed, rendered, or required to carry other files alongside it — the way `main` does. If a candidate value is just a label for what the note is about, it belongs in `category` instead.

# type vs. category

* `type` is required, structural, and (mostly) about the file's role in the bundle machinery — `main` changes what else a folder needs (`index.md`, `log.md`); other values are just labels a note carries.
* `category` is optional and content-facing — it says what *kind of thing* the note documents (`project`, `book`, `article`, `meta`), regardless of `type`.
* A note's `type` and `category` answer different questions and often both apply: [Categories](/frontmatter/category.md) is `type: note, category: meta` — a plain note (`type`) about a meta subject (`category`).

# Conventions

* `type` MUST be present and non-empty on every concept file — this is the one thing that breaks conformance if missing (per spec-okf.md's conformance rules).
* `type` is open-ended and not centrally registered, like `category` — unknown values are tolerated, but reuse existing ones over inventing near-duplicates.
* `type: main` carries extra weight: any folder with a `type: main` file must also have `index.md` and `log.md` (spec-okf.md's wiki-extension rule). Only use `main` when you actually mean "this is the folder's root concept."
* `main` is never required per-folder — not every folder needs one, and subfolders don't inherit a requirement for their own just because a parent has one. It's an editorial call by the wiki owner: which folder, within a related group of subfolders, deserves to be the root. A cluster of subfolders can share one ancestor's `main` note rather than each having its own.

**In use:**

| Type | Meaning |
|------|---------|
| `main` | A folder's root/summary concept. Requires `index.md` and `log.md` in the same folder. |
| `note` | An ordinary standing note, not tied to one project. Currently the de facto default. |

**Candidates — brainstormed, not yet adopted by any note:**

| Type | Meaning |
|------|---------|
| `person` | A note about an individual. Pairs with the `resource` map form (email, ORCID, LinkedIn, …) — see [Resource](/frontmatter/resource.md). Requires a [`sensitivity`](/frontmatter/sensitivity.md) field. |
| `dataset` | A note documenting a dataset. If it contains personal information it requires a [`sensitivity`](/frontmatter/sensitivity.md) field (minimum `confidential`). |
| `reference` | A note that's mainly a pointer to something external (a bookmark, a tool) plus light commentary, as opposed to original writing. |
| `draft` / `stub` | An intentionally incomplete note — a placeholder or in-progress piece, flagged so it can be excluded from search or index generation. |
| `template` | A reusable skeleton for creating new notes of a given shape. Would need its own conformance carve-out, since templates don't have real content. |
| `redirect` | A thin note whose only job is pointing at another note, for renamed or merged concepts, to avoid dead links. |

Adopt a candidate by actually using it on a note, then move it from "Candidates" to "In use" here.

# Guidance

* Default new notes to `type: note` unless the note is specifically a folder's root concept (`main`), clearly matches an adopted candidate above, or needs a genuinely new structural role.
* Don't use `type` to encode content classification (project vs. book vs. article) — that's what `category` is for; keep `type` about structural role.
* If you introduce a new `type` value, add it to the table above (as a candidate first, then move it to "in use" once a note actually carries it) so the vocabulary stays discoverable.

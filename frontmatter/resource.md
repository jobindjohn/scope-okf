---
type: topic
category: meta
title: Resource
description: How the resource field records a canonical URI for the underlying asset a concept documents.
tags: [okf, meta, resource]
timestamp: 2026-07-11T00:00:00Z
authorship: 1
---

Notes on the `resource` field, expanding on its definition in [spec-okf.md](/spec-okf.md).

# What `resource` is for

`resource` records a canonical URI for the asset a concept note is *about* or derived from — the book's ISBN/URL, the article's original link, the paper's DOI, the repo the note documents. It answers "where does the real thing live?" A note without an external counterpart (e.g. a pure `topic` or `meta` note) usually has no `resource`.

# resource vs. other fields

* `type` says what role the file plays in the bundle; `category` says what kind of thing the note is; `resource` says where the underlying, non-wiki asset can be found.
* Links inside the note body (`[title](path)`) point to other concepts *within* the wiki. `resource` points *outside* the wiki, to the original source.
* A note can have both: `resource` for the canonical external link, and body links to related internal concepts.

# Conventions

* `resource` is optional, like `category` and `tags` — missing values don't break conformance (per spec-okf.md's conformance rules).
* Use a single, stable URI — prefer a DOI or permanent URL over a search-result or session-specific link.
* Prefer the most canonical identifier available: DOI > publisher/official URL > archive/mirror URL.
* Write it as a plain string, not a markdown link: `resource: https://example.com/thing`, not `resource: "[thing](https://example.com/thing)"`.
* If the asset moves or the link rots, update `resource` in place rather than leaving a dead link — treat this like any other content update (bump `timestamp` too).

# Guidance

* Set `resource` on `article`- and `book`-category notes whenever the source has a stable URI — this is where it earns its keep most.
* Don't invent a `resource` for concepts that don't have one; leave the field out rather than pointing to something tangential.
* If a note documents multiple sources, put the primary/canonical one in `resource` and link the rest in the body.

---
type: note
category: meta
title: Resource
description: How the resource field records canonical URI(s) for the underlying asset a concept documents.
tags: [okf, meta, resource]
timestamp: 2026-07-11T23:30:00Z
llm:
  authorship: 2
  review: reviewed
---

Notes on the `resource` field, expanding on its definition in [spec-okf.md](/spec-okf.md).

# What `resource` is for

`resource` records where the real, non-wiki asset a concept note is *about* or derived from lives — the book's ISBN/URL, the article's original link, the paper's DOI, the repo the note documents, or a person's contact points and profiles. It answers "where does the real thing live?" A note without an external counterpart (e.g. a plain `category: meta` note like this one) usually has no `resource`.

`resource` takes one of two forms:

* **Single URI** (the common case) — one canonical link: `resource: https://example.com/thing`.
* **Map of named URIs** — when a concept has more than one canonical link. Two recurring cases:

A `type: person` note, where each key is a different contact point or profile:

```yaml
resource:
  email: mailto:jane@example.com
  orcid: https://orcid.org/0000-0000-0000-0000
  linkedin: https://linkedin.com/in/jane
  bluesky: https://bsky.app/profile/jane.bsky.social
  phone: tel:+1-555-0100
```

An `article`-category note, where each key is a different venue or index the same paper appears under:

```yaml
resource:
  doi: https://doi.org/10.xxxx/yyyy
  pmid: https://pubmed.ncbi.nlm.nih.gov/12345678
  journal: https://journal-site.example.com/article
  proceedings: https://conference.example.com/proceedings/paper
  preprint: https://arxiv.org/abs/xxxx.xxxxx
```

# resource vs. other fields

* `type` says what role the file plays in the bundle; `category` says what kind of thing the note is; `resource` says where the underlying, non-wiki asset can be found.
* Links inside the note body (`[title](path)`) point to other concepts *within* the wiki. `resource` points *outside* the wiki, to the original source.
* A note can have both: `resource` for the canonical external link, and body links to related internal concepts.

# Conventions

* `resource` is optional, like `category` and `tags` — missing values don't break conformance (per spec-okf.md's conformance rules).
* Use single, stable URIs — prefer a DOI or permanent URL over a search-result or session-specific link; prefer the most canonical identifier available: DOI > publisher/official URL > archive/mirror URL.
* Write single values as a plain string, not a markdown link: `resource: https://example.com/thing`, not `resource: "[thing](https://example.com/thing)"`.
* For the map form, use URI schemes where they exist (`mailto:` for email, `tel:` for phone) rather than bare addresses — it keeps every value a genuine URI, not just the web ones.
* Map keys are open-ended and not centrally registered, same tolerance as `category` values — but reuse common keys rather than inventing synonyms, so notes stay comparable. Seen so far: `email`, `phone`, `orcid`, `linkedin`, `bluesky`, `github`, `website`, `mastodon` (people); `doi`, `pmid`, `journal`, `proceedings`, `preprint`, `arxiv` (articles).
* If the asset moves or a link rots, update `resource` in place rather than leaving a dead link — treat this like any other content update (bump `timestamp` too).

# Guidance

* Set `resource` on `article`- and `book`-category notes whenever the source has a stable URI — the single-URI form when there's one clear canonical link, the map form when the same work has several (DOI, PMID, journal page, conference proceedings, preprint).
* Use the map form on `type: person` notes to collect contact points and profiles (email, phone, ORCID, LinkedIn, Bluesky, …) in one place rather than scattering them through the note body.
* Don't invent a `resource` for concepts that don't have one; leave the field out rather than pointing to something tangential.
* When a note has one obviously primary link and the rest are secondary mirrors or reprints, a single `resource` string plus body links is fine — reach for the map form once there are several links worth naming individually, not just because more than one exists.

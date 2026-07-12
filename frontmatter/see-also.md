---
type: note
id: 20260711-see2
category: meta
title: See also
description: How the see-also field records related notes as a machine-readable relation, complementing inline body links.
tags: [okf, meta, see-also, links]
timestamp: 2026-07-11T23:30:00Z
llm:
  authorship: 2
  review: reviewed
see-also: [/frontmatter/id.md, /frontmatter/superseded_by.md]
---

Notes on the `see-also` field, expanding on its definition in [spec-okf.md](/spec-okf.md).

# What `see-also` is for

`see-also` lists related concepts in the frontmatter, as a machine-readable relation that complements the ordinary markdown links in a note's body. It answers "what other notes are related to this one?" in a form an agent can traverse without parsing prose.

Body links (`[title](path)`) are great for humans reading a note top to bottom, but they're buried in sentences and their meaning is implicit. A `see-also` list gives agents an explicit, greppable relation edge between notes — useful for building navigation, backlinks, and knowledge-graph traversal.

# Form

```yaml
see-also: [/frontmatter/id.md, /frontmatter/superseded_by.md]
```

* A YAML list of bundle-relative paths (preferred) or [`id`](/frontmatter/id.md)s.
* Points at genuinely related concepts — siblings, prerequisites, alternatives — not at every note mentioned in passing.

# see-also vs. other links

* Body links point *into* the wiki inline, in reading order; `see-also` collects the important relations up front, in frontmatter, for machines.
* [`resource`](/frontmatter/resource.md) points *outside* the wiki to the underlying source; `see-also` points to other concepts *within* it.
* [`superseded_by`](/frontmatter/superseded_by.md) is a specific, directional "replaced by" relation; `see-also` is the general, undirected "related to" one.

# Conventions

* `see-also` is optional, and unresolvable entries are tolerated (per spec-okf.md's conformance rules) — but keep it accurate.
* Keep it short — a handful of the most relevant notes, not an exhaustive dump. If everything is related, nothing is.
* The relation is conceptually two-way; there's no requirement that the other note link back, but adding the reciprocal `see-also` makes traversal more reliable.
* Prefer paths for readability; use `id`s where the target is likely to be renamed or moved.

# Guidance

* Add a `see-also` when a reader who found this note would plausibly want another specific note next.
* (For LLMs) Use `see-also` edges to navigate the wiki and to suggest related notes, but don't infer relationships that aren't there — only what's listed or clearly linked.
* Don't duplicate `superseded_by` in `see-also`; the successor is already reachable through the more specific field.

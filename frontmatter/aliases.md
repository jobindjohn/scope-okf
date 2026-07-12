---
type: note
id: 20260712-alia
category: meta
title: Aliases
description: How the aliases field gives a note extra names for name-based links, matched at the lowest resolver tier.
tags: [okf, meta, aliases, linking]
timestamp: 2026-07-12T00:00:00Z
llm:
  authorship: 2
  review: unreviewed
see-also: [/spec-okf.md, /frontmatter/id.md, /frontmatter/see-also.md]
---

Notes on the `aliases` field, expanding on its definition in [spec-okf.md](/spec-okf.md) and its role in [Linking](/spec-okf.md#linking).

# What `aliases` is for

`aliases` is an optional list of alternative names for a note. It exists to serve name-based links: a wikilink like `[[resource]]` resolves by trying a note's `id`, then its filename basename, then its `title` and `aliases`. Aliases let a note be reached by names other than its filename — an old name it used to have, a common abbreviation, a spelled-out or hyphenated variant — without renaming the file.

```yaml
aliases: [resource-field, links-field]
```

# Where it sits in resolution

Aliases are matched at the **lowest** resolver tier, together with `title` and below the filename basename. Two consequences follow:

* An alias **never overrides a real basename.** If `[[links]]` matches a `links.md` file, that file wins even if another note carries `links` as an alias — so adding an alias can't silently shadow an existing note's filename.
* Aliases are as collision-prone as titles. Two notes sharing an alias are ambiguous and fall to the tie-break defined in [Linking](/spec-okf.md#linking) (nearest note first — same folder, then widening outward). Keep aliases distinctive.

Name matching is case-folded, and `-`, space, and `_` are treated as equivalent, so `resource field`, `resource-field`, and `resource_field` are the same alias.

# aliases vs. id vs. title

* [`id`](/frontmatter/id.md) is the one stable, rename-proof identifier — opaque and singular. Use it when a reference must never break.
* `title` is the single human display name.
* `aliases` are *additional* human names for reaching the note. A note has one `title` but may carry several aliases.

# Conventions

* `aliases` is optional and open-ended — missing values don't break conformance (per spec-okf.md's conformance rules), same as `tags`.
* Written as a YAML flow list: `aliases: [old-name, abbrev]`.
* Add an alias when people (or you) genuinely refer to the note by more than one name — not to pad reachability. If everything has five aliases, collisions multiply and the tie-break does more work.
* Prefer giving a note a stable [`id`](/frontmatter/id.md) over a thicket of aliases when the goal is durable links; aliases are for human-friendly names, not rename-proofing.

# Guidance

* (For LLMs) When resolving a `[[name]]`, follow the resolver order in [Linking](/spec-okf.md#linking) exactly — `id` → basename → `title`/`aliases` — and treat an ambiguous or unresolvable name as tolerated, not an error.
* Record an alias when renaming a note, so old name-based links keep resolving until they're hardened.

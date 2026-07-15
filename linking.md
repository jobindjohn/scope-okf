---
type: note
id: 20260712-link
category: meta
title: Linking
description: How notes reference each other — path links and Obsidian-style wikilinks resolved by one tolerant algorithm (id → basename → title/aliases).
tags: [okf, meta, linking, wikilinks, links]
timestamp: 2026-07-12T00:00:00Z
llm-authorship: 2
llm-review: reviewed
see-also: [/scope-okf.md, /frontmatter/aliases.md, /frontmatter/id.md, /frontmatter/see-also.md]
---

Notes on how this wiki links notes together, expanding the summary in [scope-okf.md](/scope-okf.md).

# Two link forms

Notes reference each other in two spellings that resolve to the same thing. Both are wiki extensions over plain markdown; neither is required for conformance.

**Path links (the portable form).** An ordinary markdown link to a bundle-relative path — leading `/` from the wiki root, `.md` extension included: `[index](/index.md)`, `[Resource](/frontmatter/resource.md)`. This is real markdown: it renders and degrades gracefully in any tool, and remains the preferred form for anything shared outside a wikilink-aware editor.

**Wikilinks (the ergonomic form).** A name-based reference in double brackets, no path or extension required: `[[resource]]`. Wikilinks are an authoring convenience — they are *not* real markdown and show up as literal `[[resource]]` text in tools that don't understand them. A wikilink is always reducible to a path link (see [Hardening](#hardening) below), so the durable, shareable form of a bundle stays plain markdown with `[[ ]]` as an input layer on top.

Both forms, plus the `id`/path references already accepted by [`see-also`](/frontmatter/see-also.md) and [`superseded_by`](/frontmatter/superseded_by.md), resolve through **one algorithm**. A wikilink is a third spelling of the existing reference, not a separate subsystem.

# The resolver

Any reference — a body `[[name]]`, or a bare string in `see-also`/`superseded_by` — resolves to at most one note by trying, in order:

1. **`id`** — exact match against a note's [`id`](/frontmatter/id.md). First, because a tooling-hardened reference (`[[<id>]]`) must resolve deterministically to its target even if a later file coincidentally shares the same name. Since `id`s are recommended to be opaque slugs, this branch is effectively machine-facing and rarely collides with human-typed names.
2. **Filename basename** — the note's filename without the `.md` extension (e.g. `resource` for `frontmatter/resource.md`). The common, human-facing case.
3. **`title`, then `aliases`** — display names. Lowest precedence because they are optional, mutable, and collision-prone.

First match wins. A reference that resolves to nothing, or stays ambiguous after the disambiguation rules below, is **tolerated** — consistent with existing broken-link tolerance — but resolution is otherwise **deterministic**, never arbitrary.

**Normalization.** Name matching (basename, `title`, `aliases`) is **case-folded**, and `-`, space, and `_` are treated as equivalent, so `[[see also]]`, `[[See-Also]]`, and `[[see_also]]` all match `see-also.md`. This keeps a bundle behaving the same across case-sensitive and case-insensitive filesystems.

**Excluded from the resolver.** Reserved filenames (`index.md`, `log.md`) and everything under the reserved `.llm/` folder are not concepts and never enter the name index, so `[[index]]` has no target. A `type: main` file (named after its folder, e.g. `scope-okf.md`) *is* an ordinary target: `[[scope-okf]]` resolves normally.

# Wikilink syntax

* **Bare name:** `[[resource]]`.
* **Path-qualified name:** `[[frontmatter/resource]]` — **bundle-root-relative, no leading `/`, no `.md` extension**, used to disambiguate. Never current-file-relative: file-relative resolution would reintroduce the path-dependence that `id` exists to remove.
* **Display text:** `[[resource|the resource field]]` — the pipe separates target from rendered text.
* **Heading anchor:** `[[resource#Map form]]` — targets a heading within the note, matched on the heading's exact text (Obsidian behavior).
* **Embed:** `![[resource]]` — transcludes the target's body. Optional and the biggest commitment: embeds degrade worst in non-aware tools, so treat them as advanced rather than core.

# Collisions and disambiguation

When a bare name matches more than one note, use **shortest-unique-path disambiguation**: write the bare name when it's unique, and add just enough leading path to make it unique when it isn't — `[[resource]]` if there's one, `[[frontmatter/resource]]` if two `resource.md` files exist in different folders.

Because a plain-file bundle has no interactive UI, adding a note can *retroactively* make an existing bare link ambiguous. Two safeguards keep this in check:

* **Deterministic tie-break.** When a bare name is still ambiguous, resolution is not arbitrary: prefer a note in the **same folder** as the linking note, then widen progressively — parent folders and their siblings outward — taking the nearest match.
* **Hardening** removes the ambiguity permanently.

Ambiguous links are tolerated but never nondeterministic.

# `aliases`

The optional [`aliases`](/frontmatter/aliases.md) frontmatter field lists alternative names for a note, matched at the `title` tier of the resolver:

```yaml
aliases: [resource-field, links-field]
```

Aliases never override a real basename (basename is higher precedence), so adding one can't silently shadow another note's filename. Two notes sharing an alias are ambiguous and fall to the tie-break, like any other name collision.

# Body syntax vs. frontmatter syntax

One resolver, two surface syntaxes:

* **Body** uses the `[[ ]]` brackets: `See [[resource]].`
* **Frontmatter** (`see-also`, `superseded_by`) keeps writing **bare strings**, not brackets — `[[ ]]` collides with YAML's nested-sequence syntax. The same resolver applies to those strings:

  ```yaml
  see-also: [resource, /frontmatter/id.md, 20260711-see2]
  ```

  Each entry is treated as a path if it looks like one, otherwise run through the resolver as a name or `id`.

# Hardening

A wikilink can be *hardened* into portable markdown by resolving it once and recording the result — carrying the resolved `id` so it survives renames, and optionally rewriting into a real path link:

```
[[resource]]   →   [resource](/frontmatter/resource.md)
```

Hardening is what lets `[[ ]]` be an ergonomic input form without eroding the bundle's promise that its files are correctly linked in any tool. Tooling MAY harden on save; the spec neither requires nor forbids it.

# Gotchas

* **Pipes break tables.** `[[note|display]]` inside a markdown table cell breaks table parsing (the spec is table-heavy). Escape the pipe as `\|`, or use a path link inside tables.
* **Non-aware renderers** show `[[ ]]` and `![[ ]]` literally. Prefer path links, or harden, for anything shared outside a wikilink-aware tool.

# Conformance

Wikilinks, `aliases`, hardening, and the resolver are all **tolerant wiki extensions**. Unresolvable or ambiguous references — like broken path links and dangling `see-also`/`superseded_by`/`id` references today — do **not** break conformance, and no new required field or reserved name is introduced. (Pure filename-basename resolution would have needed a "concept basenames are unique" rule; the hybrid resolver here deliberately avoids that.)

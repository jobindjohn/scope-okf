---
type: note
id: 20260711-v1s8
category: meta
title: Visibility
description: How the visibility field controls whether a note is indexed, searched, and surfaced by agents.
tags: [okf, meta, visibility]
timestamp: 2026-07-11T23:30:00Z
llm:
  authorship: 2
  review: reviewed
see-also: [/frontmatter/sensitivity.md, /index.md]
---

Notes on the `visibility` field, expanding on its definition in [scope-okf.md](/scope-okf.md).

# What `visibility` is for

`visibility` controls how a note is *surfaced* within the wiki — whether it shows up in `index.md`, in routine search, and in what an agent volunteers. It answers "should this note be exposed here?" It is orthogonal to [`sensitivity`](/frontmatter/sensitivity.md), which is about how private the *content* is.

# Scale

| Value | Meaning |
|-------|---------|
| `listed` | Default. Appears in `index.md` and normal search; agents may surface it freely. |
| `unlisted` | Readable if something links to it, but excluded from index generation and routine search. For stubs, drafts, and supporting notes that shouldn't clutter navigation. |
| `hidden` | Agents should not surface or expose the note unless the human asks for it by name. For private or set-aside notes. |

Absent means `listed`.

# visibility vs. sensitivity

* `visibility` is operational — where and whether the note appears.
* `sensitivity` is about the content — how protected the information is.

A note can be `sensitivity: confidential, visibility: listed` (surfaced internally but handled carefully) or `sensitivity: public, visibility: unlisted` (harmless but kept out of the index). Use `sensitivity` for access/sharing decisions and `visibility` for surfacing decisions; don't collapse one into the other.

# Conventions

* `visibility` is optional — a missing value means `listed`, and unknown values are tolerated (per scope-okf.md's conformance rules).
* `unlisted` notes are still first-class concepts — they just don't appear in generated indexes. Consumers that build `index.md` should skip `unlisted` and `hidden` notes.
* `hidden` is a surfacing hint, not an access control — pair it with `sensitivity` when the concern is privacy, not just clutter.

# Guidance

* (For LLMs) Don't volunteer `hidden` notes in summaries, indexes, or answers unless the human names them. Respect `unlisted` by leaving those notes out of any index you generate.
* Reach for `unlisted` for drafts and scaffolding; reach for `hidden` when a note should stay out of an agent's default view of the wiki.
* Set `visibility` explicitly only when you want non-default behaviour — most notes need nothing here.

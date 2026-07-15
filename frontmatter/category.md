---
type: note
category: meta
title: Categories
description: How the category field is used to classify concepts in this wiki.
tags: [okf, meta, category]
timestamp: 2026-07-11T23:30:00Z
llm-authorship: 2
llm-review: reviewed
---

Notes on the `category` field, expanding on its definition in [scope-okf.md](/scope-okf.md).

# What `category` is for

`category` classifies *what kind of content* a concept note is — a project, a book, a topic, an article. It answers "what is this note, as a thing?" rather than "what is this note about?" (that's `tags`) or "what schema does it follow?" (that's `type`).

# category vs. type vs. tags

* `type` is the required, structural field — it says what role the file plays in the bundle (e.g. `main` for a folder root). It's about frontmatter conformance, not content.
* `category` is optional and content-facing — it says what kind of thing the note documents.
* `tags` are free-form keywords for search and cross-cutting association; a note can have many, `category` is normally just one.

# Conventions

`category` is open-ended and not centrally registered — unknown or missing values don't break conformance (per scope-okf.md's conformance rules). That said, keeping the vocabulary small and reused makes the wiki more navigable. Categories seen so far in this wiki:

| Category  | Meaning                                                   |
|-----------|------------------------------------------------------------|
| `project` | A body of work with its own goals and folder, e.g. `scope-okf` itself. |
| `book`    | Notes about a book — summary, highlights, reactions.       |
| `topic`   | A standing subject area note, not tied to one project.     |
| `article` | Notes on or reproductions of a single article/paper/post.  |
| `meta`    | Notes about the wiki's own conventions (like this one).    |

# Guidance

* Prefer an existing category over inventing a new one, unless the note genuinely doesn't fit.
* Use `category` on any concept, not only folder-root (`type: main`) notes.
* Don't leave `category` blank just because a note is small — a one-line `category: article` is more useful long-term than nothing.
* If a new recurring category emerges, add it to the table above so it's discoverable.

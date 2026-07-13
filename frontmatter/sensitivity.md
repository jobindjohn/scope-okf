---
type: note
id: 20260711-s3n5
category: meta
title: Sensitivity
description: How the sensitivity field classifies how private or protected a note's content is — required on person and personal-data dataset notes.
tags: [okf, meta, sensitivity, privacy]
timestamp: 2026-07-11T23:30:00Z
llm:
  authorship: 2
  review: reviewed
sensitivity: public
see-also: [/frontmatter/visibility.md, /frontmatter/type.md]
---

Notes on the `sensitivity` field, expanding on its definition in [scope-okf.md](/scope-okf.md).

# What `sensitivity` is for

`sensitivity` classifies how private or protected a note's content is, so both humans and agents can make sharing and access decisions without reading the whole note. It answers "how careful do I need to be with this?" It is about the *content*, not about how the note is surfaced — that is [`visibility`](/frontmatter/visibility.md).

Once an agent can traverse the whole wiki, it needs to know which notes hold personal or protected data before it surfaces, quotes, or transmits them. `sensitivity` is that marker.

# Scale

| Value | Meaning |
|-------|---------|
| `public` | Nothing sensitive or personal; safe to share freely. |
| `internal` | Not for public distribution, but contains nothing personally sensitive. |
| `confidential` | Contains personal data (PII) or other sensitive information; access and sharing should be limited. The **minimum** level for any note holding personal information. |
| `restricted` | Special-category or high-risk data — health, financial, biometric, credentials, government IDs, or otherwise legally protected. Agents must not surface or transmit these without explicit human authorization. |

The scale is ordered from least to most sensitive. When in doubt between two levels, pick the higher one.

# When it's required

`sensitivity` is optional on most notes, but **required** (a conformance rule — see scope-okf.md §Conformance) on:

* every `type: person` note — these hold contact details and identifiers by design; minimum `confidential`;
* every `type: dataset` note that contains personal information — minimum `confidential`, or `restricted` if the data is special-category.

A `type: dataset` note with no personal data (e.g. a table of public statistics) doesn't require the field, though `public`/`internal` is still worth setting.

# sensitivity vs. visibility

* `sensitivity` describes the *content*: how private or protected the information is. It drives access, sharing, and transmission decisions.
* [`visibility`](/frontmatter/visibility.md) describes *exposure*: whether the note appears in indexes and search and whether an agent may surface it.

They correlate but aren't identical — a `confidential` note might still be `listed` internally, and a `public` note might be `unlisted` simply because it's a stub. Set both when they diverge.

# Conventions

* Values are a small controlled vocabulary — unlike `tags`, don't invent new ones; if a note doesn't fit, it's almost always `restricted`.
* Set `sensitivity` on any note holding personal or protected data even where it isn't strictly required — the marker is what lets an agent handle the note safely.
* Re-evaluate on edit: if a note gains personal or protected content, raise its level (and bump `timestamp`).

# Guidance

* (For LLMs) Never surface, quote, or transmit a `restricted` note, and treat `confidential` notes as need-to-know, unless the human has explicitly authorized it in the current session.
* (For LLMs) When you create a `type: person` note or a `type: dataset` note with personal data, set `sensitivity` in the same pass — omitting it breaks conformance.
* Prefer over-classifying to under-classifying: the cost of marking something `confidential` that turns out to be public is low; the reverse can leak data.

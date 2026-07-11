---
type: topic
category: meta
title: Authorship
description: How the authorship field records how much of a concept's text came from a human vs. an LLM.
tags: [okf, meta, authorship]
timestamp: 2026-07-11T00:00:00Z
authorship: 1
---

Notes on the `authorship` field, expanding on its definition in [spec-okf.md](/spec-okf.md).

# What `authorship` is for

`authorship` records how a concept's text was produced — how much came from an LLM versus a human, and who did the last pass. It's a provenance record, not a quality or accuracy signal: a `4` (fully human) note can still be wrong, and a `1` (fully LLM) note can still be correct.

# Scale

| Value | Meaning |
|-------|---------|
| `1` | Fully LLM-generated. No human edit. |
| `2` | LLM-drafted, human-edited. An LLM produced the text; a human revised it. |
| `3` | Human-drafted, LLM-edited. A human produced the text; an LLM revised it. |
| `4` | Fully human-authored. No LLM involvement. |

Higher numbers mean more of the final text is directly attributable to a human hand — but `2` and `3` are not ranked against each other; they describe different workflows, not degrees of trust.

# Conventions

* `authorship` is optional, like `category` and `tags` — missing values don't break conformance (per spec-okf.md's conformance rules).
* The field reflects the *current* state of the text, not its full edit history. A note that went LLM-draft → human-edit → further LLM-edit should be set to whatever level best describes the latest substantive pass, not left at its earliest value.
* For notes that go through several rounds of back-and-forth (LLM and human alternating edits), pick the value describing who did the more recent structural or substantive work — pure copyedits don't need to flip the value.
* Set or update `authorship` at the same time you'd update `timestamp` — when a note's authorship materially changes, both should change together.

# Guidance

* Default to setting `authorship` on any note produced with LLM involvement, even partial. Silence isn't a claim of human authorship — but making it explicit is more useful than leaving it unspecified.
* Don't retroactively backfill `authorship` on old notes unless you're confident of how they were produced.
* (For LLMs) When you create or edit a note, set `authorship` to reflect your own involvement honestly — don't default to `4` just because the wiki owner asked for the change.

---
type: note
category: meta
title: Authorship
description: How the authorship field records how much of a concept's text came from a human vs. an LLM, and how directly.
tags: [okf, meta, authorship]
timestamp: 2026-07-11T20:00:00Z
authorship: 2
---

Notes on the `authorship` field, expanding on its definition in [spec-okf.md](/spec-okf.md).

# What `authorship` is for

`authorship` records how a concept's text was produced — how much came from an LLM versus a human, how directly, and who did the last pass. It's a provenance record, not a quality or accuracy signal: a `5` (fully human) note can still be wrong, and a `1` (fully LLM) note can still be correct.

# Scale

| Value | Meaning |
|-------|---------|
| `1` | Fully LLM-generated, autonomous. No human feedback loop, no human edit. |
| `2` | LLM-generated, human-directed. Human gives feedback or instructions across one or more rounds; the LLM implements them. Text stays fully LLM-penned — the human never edits the prose directly. |
| `3` | LLM-drafted, human-edited. An LLM produced the text; a human revised it directly. |
| `4` | Human-drafted, LLM-edited. A human produced the text; an LLM revised it. |
| `5` | Fully human-authored. No LLM involvement. |

Higher numbers mean more of the final text is directly attributable to a human hand — but `3` and `4` are not ranked against each other; they describe different workflows, not degrees of trust. `1` and `2` are both fully LLM-penned text; they differ in editorial control, not hand-attribution — `2` means the human steered revisions through feedback even though they never typed into the note themselves.

# Conventions

* `authorship` is optional, like `category` and `tags` — missing values don't break conformance (per spec-okf.md's conformance rules).
* The field reflects the *current* state of the text, not its full edit history. A note that went LLM-draft → human-edit → further LLM-edit should be set to whatever level best describes the latest substantive pass, not left at its earliest value.
* For notes that go through several rounds of back-and-forth (LLM and human alternating edits), pick the value describing who did the more recent structural or substantive work — pure copyedits don't need to flip the value.
* `1` vs. `2` turns on whether there was *any* round of human feedback that led to a revision, not on how the note started. A single up-front instruction with no follow-up ("write a note about X") is still `1`. As soon as a human reacts to a draft and the LLM revises in response — even just "rename this" or "add a section on Y" — it's `2`, not `1`.
* Set or update `authorship` at the same time you'd update `timestamp` — when a note's authorship materially changes, both should change together.

# Guidance

* Default to setting `authorship` on any note produced with LLM involvement, even partial. Silence isn't a claim of human authorship — but making it explicit is more useful than leaving it unspecified.
* Don't retroactively backfill `authorship` on old notes unless you're confident of how they were produced.
* (For LLMs) When you create or edit a note, set `authorship` to reflect your own involvement honestly — don't default to `5` just because the wiki owner asked for the change. Most LLM-assisted notes in a conversational wiki like this one are `2`, not `1`: if the human reacted to anything you wrote before this pass, it's a feedback loop.

---
type: note
id: 20260711-ll0m
category: meta
title: LLM
description: How the llm field records an LLM's relationship to a note — authorship, review/trust status, and whether agents may edit it.
tags: [okf, meta, llm, authorship]
timestamp: 2026-07-11T23:30:00Z
llm:
  authorship: 2
  review: reviewed
see-also: [/frontmatter/type.md, /llm.md, /frontmatter/sensitivity.md]
---

Notes on the `llm` field, expanding on its definition in [spec-okf.md](/spec-okf.md). For the separate `.llm/` provenance-sidecar system this field points into, see [LLM provenance](/llm.md).

# What `llm` is for

`llm` is a map that records an LLM's relationship to a note in one place: how much of the text an LLM produced (`authorship`), whether a human has vetted it (`review`), and whether agents may edit it without sign-off (`human-only-lock`). It replaces the earlier standalone `authorship` scalar — that value now lives at `llm.authorship`.

The block groups the questions that matter once experts and agents co-author a wiki: *who wrote this*, *can I trust it yet*, and *am I allowed to change it*. Keeping them together means a reader — human or agent — gets the whole provenance-and-trust picture from a single field.

```yaml
llm:
  authorship: 2
  review: reviewed
  human-only-lock: false
```

# Keys

## `authorship` (1–5)

Records how a note's text was produced — how much came from an LLM versus a human, and how directly. It is a provenance record, not a quality signal: a `5` (fully human) note can still be wrong, a `1` (fully LLM) note can still be correct. That is exactly why `review` exists as a separate key.

| Value | Meaning |
|-------|---------|
| `1` | Fully LLM-generated, autonomous. No human feedback loop, no human edit. |
| `2` | LLM-generated, human-directed. Human gives feedback across one or more rounds; the LLM implements them. Text stays fully LLM-penned — the human never edits the prose directly. |
| `3` | LLM-drafted, human-edited. An LLM produced the text; a human revised it directly. |
| `4` | Human-drafted, LLM-edited. A human produced the text; an LLM revised it. |
| `5` | Fully human-authored. No LLM involvement. |

Higher numbers mean more of the final text is directly attributable to a human hand — but `3` and `4` are not ranked against each other; they describe different workflows, not degrees of trust. `1` and `2` are both fully LLM-penned; they differ in editorial control, not hand-attribution. `1` vs. `2` turns on whether there was *any* round of human feedback that led to a revision, not on how the note started: a single up-front instruction with no follow-up ("write a note about X") is still `1`; as soon as a human reacts to a draft and the LLM revises — even just "rename this" — it's `2`.

When `authorship` is `1`–`4`, the note also carries a detailed provenance record (the prompt, the model): inline for `1`, in a `.llm/` sidecar for `2`–`4`. See [LLM provenance](/llm.md) for that mechanism, including the `1`→`2` migration.

## `review` (unreviewed | reviewed | verified)

The trust signal, deliberately separate from `authorship`. Authorship says who produced the text; `review` says whether a human has vetted it.

| Value | Meaning |
|-------|---------|
| `unreviewed` | No human has vetted the note. The default when `review` is absent. |
| `reviewed` | A human has read the note and approved it as a whole. |
| `verified` | A human has checked the note's specific claims or facts for correctness, not just read it. |

`review` can be set even when a note has no LLM involvement (`authorship: 5`) — a hand-written note can still be unreviewed, and a stale hand-written fact is no safer than an agent-written one. It lives inside `llm` because vetting is most urgent for LLM-produced text, but it is genuinely a content-trust signal for any author.

## `human-only-lock` (true | false)

Tells agents whether they may edit the note autonomously. `true` means an agent MUST get explicit human sign-off before changing the note; `false` (or absent) means agents may edit it under the normal conventions. Use it on notes whose wording is load-bearing — the spec itself ([spec-okf.md](/spec-okf.md) carries `human-only-lock: true`), policy notes, or anything where an unattended agent edit would be costly.

# Conventions

* Every key is optional, like `category` and `tags` — a missing `llm` block, or missing sub-keys, don't break conformance (per spec-okf.md's conformance rules). An out-of-range `authorship` or unknown `review` value is tolerated too.
* The block reflects the *current* state of the note, not its full history. Update `authorship` to whatever level best describes the latest substantive pass; move `review` back to `unreviewed` if a note is materially rewritten after being reviewed.
* Set or update `llm` at the same time you'd update `timestamp` — when authorship, review status, or the lock materially changes, bump both together.
* Don't let `llm` become a junk drawer. It is for the LLM's involvement and what agents may do here. Content-classification goes in `category`, subject keywords in `tags`, and note-level concerns that apply regardless of author — freshness, sensitivity, superseding — in their own fields.

# Guidance

* (For LLMs) When you create or edit a note, set `llm.authorship` to reflect your own involvement honestly — don't default to `5` just because the wiki owner asked for the change. Most LLM-assisted notes in a conversational wiki like this one are `2`, not `1`.
* (For LLMs) Set `review: unreviewed` on notes you produce unless the human has actually vetted them in the same session. Don't self-certify `reviewed` or `verified` — those are the human's to assert.
* (For LLMs) Treat `human-only-lock: true` as binding: propose the change, wait for explicit go-ahead, then write.
* Don't retroactively backfill `llm` on old notes unless you're confident how they were produced and vetted.

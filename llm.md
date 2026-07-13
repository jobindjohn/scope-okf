---
type: note
category: meta
title: LLM provenance
description: How LLM-authored notes record the way their text was produced — inline for authorship 1, a type:llm-log sidecar under .llm/ for 2–4.
tags: [okf, meta, authorship, llm]
timestamp: 2026-07-11T23:30:00Z
llm:
  authorship: 2
  review: reviewed
see-also: [/frontmatter/llm.md, /scope-okf.md]
---

Notes on how this wiki records LLM provenance, expanding the summary in [scope-okf.md](/scope-okf.md) and complementing the [LLM field](/frontmatter/llm.md).

# What LLM provenance is for

The [`llm.authorship`](/frontmatter/llm.md) value is a single scalar: it says *that* an LLM was involved and roughly how directly, but not *what* was asked or *which* model produced the text. LLM provenance fills that gap. For any note with LLM involvement in its text — `llm.authorship` `1` through `4` — the wiki keeps a durable record of how that text was produced: the prompt, the model, and the resulting authorship level.

This is a provenance record, not a quality signal and not a changelog. Its job is to let a later reader reconstruct how a note came to say what it says. Its relationship to [`log.md`](/scope-okf.md) is defined under *Boundary with `log.md`* below.

# Where provenance lives

Where the record lives depends on the authorship level:

* **`authorship: 1`** — fully autonomous: a single generating prompt, no human feedback. One interaction doesn't warrant its own file, so the provenance is recorded **inline**, as a single block at the top of the note body (below the frontmatter, above the note's content), in the same one-entry format used by the sidecar.
* **`authorship: 2`–`4`** — a human feedback loop exists. Provenance moves to a dedicated sidecar concept with `type: llm-log`, stored in a `.llm/` folder beside the note and named after the note with a `-llm` suffix.

```
projects/scope-okf/
├── scope-okf.md          # the concept — authorship: 2
├── .llm/
│   └── scope-okf-llm.md  # type: llm-log — provenance for scope-okf.md
├── index.md
└── log.md
```

## Migration from `1` to `2`

The first time a note gains a human feedback round, its provenance graduates from inline to a sidecar:

1. Create `.llm/<note>-llm.md`.
2. Move the inline `authorship: 1` block into it as the **oldest** (bottom) entry.
3. Add the new round as the newest entry on top.
4. Remove the inline block from the note body.

Provenance then lives in exactly one place. Because the inline block already uses the sidecar's single-entry format, the move is copy-paste.

# `.llm/` is a reserved system folder

`.llm/` holds provenance sidecars for notes in the same folder and is treated as system infrastructure, not part of the browsable concept tree:

* It is never listed in `index.md`.
* The "folder with a `type: main` file MUST also have `index.md` and `log.md`" rule never applies to `.llm/` itself.
* Reserved-filename semantics are **suspended inside `.llm/`**: every file there is a `type: llm-log` record, even one named `index-llm.md` or `log-llm.md`. Combined with the mandatory `-llm` suffix, this keeps every provenance file unambiguous.
* Reserved files (`index.md`, `log.md`) get **no** sidecar — they are tooling artifacts, not authored concepts.

# `llm-log` file format

A `type: llm-log` file needs only `type` in its frontmatter; `title` and a `timestamp` (the last interaction) are useful. It carries **no `authorship`** of its own — an LLM logging itself has no meaningful provenance level. Its body is a reverse-chronological list of interactions, newest first:

```markdown
---
type: llm-log
title: LLM log — scope-okf
timestamp: 2026-07-11T20:30:00Z
---

## 2026-07-11T20:30Z · claude-opus-4-8 · → authorship 2
**Prompt:** <verbatim prompt>
**Change:** one-line summary of what the model did / what changed.

## 2026-07-11T20:00Z · claude-opus-4-8 · → authorship 1
**Prompt:** <verbatim original generating prompt>
**Change:** initial generation.
```

Each entry heading carries a full ISO 8601 timestamp, the model identifier, and the resulting authorship level. Timestamps are per-entry, not date-grouped like `log.md`, because interaction order *is* the provenance. Record the prompt verbatim; summarize the change in a line.

# What does and doesn't produce an entry

* Only LLM interactions produce entries. Human-only edits generate none — those show up in git history and in `log.md`. A sidecar is therefore a partial history by design: a gap doesn't mean missing data.
* A sidecar persists even if a note is later fully rewritten by a human (`authorship: 5`). It simply stops gaining entries; provenance history is not deleted.
* Don't retroactively backfill provenance for interactions you're not sure of. Record what you can attest to and start the trail there, rather than reconstructing a history you don't have.

# Boundary with `log.md`

Provenance and the changelog never duplicate each other:

* `log.md` — folder-scoped changelog: **what** changed and **when**, human-facing.
* `.llm/<note>-llm.md` — note-scoped provenance: **how** a note's text was produced (prompt, model, resulting authorship), machine-facing.

A `log.md` entry MUST NOT reproduce prompts or model strings; it may note that a concept was LLM-revised and give its new authorship level, and stop there. An `llm-log` entry MUST NOT serve as the folder changelog.

# Conformance

LLM provenance is a recommended convention, not a conformance requirement. Missing or partial provenance — a missing inline block, an absent or incomplete `.llm/` sidecar — is tolerated exactly like any other missing optional field, and does not break OKF conformance.

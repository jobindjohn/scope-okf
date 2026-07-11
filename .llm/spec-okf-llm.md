---
type: llm-log
title: LLM log — spec-okf
timestamp: 2026-07-11T22:00:00Z
---

Provenance for [spec-okf.md](/spec-okf.md). Trail starts at the first LLM interaction recorded under this convention; earlier history predates it and is not backfilled.

## 2026-07-11T22:00Z · claude-opus-4-8 · → authorship 2
**Prompt:** "Move the LLM related notes into an llm.md folder to keep the spec-okf.md concise. start an .llm/ folder in the process also to serve as an illustration"
**Change:** Extracted the detailed LLM provenance section into a new [llm.md](/llm.md), leaving a two-paragraph summary plus a link in spec-okf.md.

## 2026-07-11T21:30Z · claude-opus-4-8 · → authorship 2
**Prompt:** Decisions from the brainstorm, ending "Keep the -llm suffix / also exempt reserved files + suspend reserved semantics inside .llm/. Write to update spec-okf.md" — i.e. add a durable LLM provenance record: type:llm-log sidecar, authorship 1 inline with 1→2 migration, .llm/ as a reserved system folder, anti-drift charter vs log.md, reverse-chronological entries.
**Change:** Added the LLM provenance section; cross-referenced it from the Structure table, the `authorship` field paragraph, and Conformance; bumped `timestamp`.

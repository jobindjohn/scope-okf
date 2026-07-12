---
type: llm-log
title: LLM log — llm
timestamp: 2026-07-11T23:30:00Z
---

Provenance for [llm.md](/llm.md).

## 2026-07-11T23:30Z · claude-opus-4-8 · → authorship 2
**Prompt:** "rework authorship into llm. Add new fields for review, visibility, id, superseded_by, see-also, / sensitivity: come up with keywords (required for notes with type:person or type:dataset with personal info) / llm: authorship 1-5, review: unreviewed/reviewed/verified, human-only-lock: true/false"
**Change:** Migrated this note's frontmatter to the `llm` map and repointed its references from the old `authorship` field/scalar to `llm.authorship` and the renamed [LLM field](/frontmatter/llm.md) doc.

## 2026-07-11T22:00Z · claude-opus-4-8 · → authorship 2
**Prompt:** "Move the LLM related notes into an llm.md folder to keep the spec-okf.md concise. start an .llm/ folder in the process also to serve as an illustration"
**Change:** Created llm.md by extracting the LLM provenance section from spec-okf.md and expanding it into a full field-style note (what it's for, inline vs. sidecar, the 1→2 migration, `.llm/` folder rules, `llm-log` format, boundary with log.md, conformance).

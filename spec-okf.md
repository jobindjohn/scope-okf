---
type: main
title: spec-okf
description: Specs for Jobin's wiki
category: project
tags: [wiki, okf, meta]
timestamp: 2026-07-11T21:30:00Z
authorship: 2
---

Root concept for this wiki. See the [index](/index.md) for what's here and the [log](/log.md) for update history. This note is the spec itself — structure and frontmatter conventions are defined below.

# Personal Wiki Spec

This wiki is a knowledge bundle conforming to the [Open Knowledge Format (OKF) v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md).

## Structure

The wiki is a directory tree of markdown files. Any `.md` file is a **concept** document, except two reserved filenames:

| Filename   | Purpose                                    |
|------------|---------------------------------------------|
| `index.md` | Optional directory listing (no frontmatter). |
| `log.md`   | Optional chronological update log.          |

A `.llm/` subfolder is also reserved: it holds LLM provenance sidecars (`type: llm-log`) for notes in the same folder. Files under `.llm/` are provenance records, not part of the browsable concept tree — see [LLM provenance](#llm-provenance) below.

## Concept documents

Every concept file MUST begin with a YAML frontmatter block containing one required field:

```yaml
---
type: <Type name>                  # REQUIRED
category: <Optional content classification>  # categories — see below
title: <Optional display name>
description: <Optional one-line summary>
resource: <Optional canonical URI, or map of named URIs, for the underlying asset(s)>
tags: [<tag>, <tag>, …]            # Optional
timestamp: <ISO 8601 datetime>     # Optional last-modified time
authorship: <1|2|3|4|5>            # Optional provenance level — see below
---
```

**Required:** `type`.

**Recommended, in priority order:** `category`, `title`, `description`, `resource`, `tags`, `timestamp`, `authorship`.

**Wiki extension — `category`:** an optional field, usable on any concept (not just folder roots), that classifies what kind of content the note is — e.g. `project`, `book`, `topic`, `article`. Open-ended, not centrally registered, same tolerance rules as `type`.

**Wiki extension — `authorship`:** an optional field recording how a note's text was produced, on a 1–5 scale: `1` fully LLM-generated/autonomous, `2` LLM-generated/human-directed (feedback loop, no direct human edits), `3` LLM-drafted/human-edited, `4` human-drafted/LLM-edited, `5` fully human-authored. See [Authorship](/frontmatter/authorship.md) for full guidance. Missing values are tolerated, same as `category`. When `authorship` is `1`–`4`, the note's provenance (prompt, model) is also recorded — inline for `1`, in a `.llm/` sidecar for `2`–`4`. See [LLM provenance](#llm-provenance) below.

**Wiki extension — `resource`:** may be either a single URI string (the common case) or a YAML map of named URIs when a concept has more than one canonical link — e.g. a `type: person` note with several contact points and profiles, or an `article`-category note indexed under several venues (DOI, PMID, journal, proceedings, preprint):

```yaml
resource:
  email: mailto:jane@example.com
  orcid: https://orcid.org/0000-0000-0000-0000
  linkedin: https://linkedin.com/in/jane
  bluesky: https://bsky.app/profile/jane.bsky.social
  phone: tel:+1-555-0100
```

Map keys are open-ended, not centrally registered — same tolerance rules as `category` and `tags`. See [Resource](/frontmatter/resource.md) for full guidance.

## Folder root concepts (`type: main`)

A folder MAY have one concept file whose `type` is `main`, marking it as the root/summary concept for that folder. The file is named after the folder itself, not `index.md`:

Not every folder needs one, and nested subfolders don't inherit a requirement for their own `type: main` just because a parent or sibling has one. Whether a given folder — or which folder within a related group of subfolders — gets a `main` note is an editorial call for the wiki owner, made when that folder represents a coherent enough unit to deserve a root/summary concept. A cluster of subfolders can share a single ancestor's `main` note as their effective root without each one having its own.

```
projects/
└── spec-okf/
    ├── spec-okf.md      # type: main — root concept for this folder
    ├── index.md
    ├── log.md
    └── ...other concepts
```

**Wiki extension rule:** any folder containing a `type: main` file MUST also have an `index.md` and a `log.md` in that same folder. (This is stricter than base OKF, which treats both files as always-optional.)

## Index files

An `index.md` MAY appear in any directory, including the wiki root, to list that directory's contents. It has **no frontmatter** — just a body grouping links under headings:

```markdown
# Section / Group Heading

* [Title 1](relative-url-1) - short description of item 1
* [Title 2](relative-url-2) - short description of item 2

# Another Section

* [Subdirectory](subdir/) - short description of the subdirectory
```

Descriptions should be pulled from the linked concept's `description` field when available. `index.md` can be generated by hand or on the fly; consumers may synthesize one if it's missing.

## Log files

A `log.md` MAY appear at any level to record that scope's update history, as a flat list of date-grouped entries, newest first:

```markdown
# Directory Update Log

## 2026-07-11
* **Update**: Added new entry for [Concept Title](/path/to/concept.md).
* **Creation**: Established this directory.

## 2026-07-01
* **Initialization**: Created foundational structure.
```

Date headings MUST use ISO 8601 `YYYY-MM-DD`. The leading bold word (`**Update**`, `**Creation**`, `**Deprecation**`, etc.) is a convention, not a requirement.

## LLM provenance

When a note has any LLM involvement in its text — `authorship` `1` through `4` — the wiki keeps a durable record of *how* that text was produced: the prompt, the model, and the resulting authorship level. This is provenance, not a changelog; its relationship to `log.md` is defined at the end of this section.

### Where provenance lives

* **`authorship: 1`** — fully autonomous, a single generating prompt, no human feedback. One interaction doesn't warrant its own file, so the provenance is recorded **inline**, as a single block at the top of the note body (below the frontmatter, above the note's content).
* **`authorship: 2`–`4`** — a human feedback loop exists. Provenance moves to a dedicated sidecar concept with `type: llm-log`, stored in a `.llm/` folder beside the note and named after the note with a `-llm` suffix:

```
projects/spec-okf/
├── spec-okf.md          # the concept — authorship: 2
├── .llm/
│   └── spec-okf-llm.md  # type: llm-log — provenance for spec-okf.md
├── index.md
└── log.md
```

**Migration (`1` → `2`).** The first time a note gains a human feedback round, create its `.llm/<note>-llm.md`, move the inline `authorship: 1` block into it as the **oldest** entry, add the new round as the newest entry on top, and remove the inline block from the note body. Provenance then lives in exactly one place.

### `.llm/` is a reserved system folder

* It is **not** part of the browsable concept tree: `.llm/` is never listed in `index.md`, and the "folder with a `type: main` file MUST also have `index.md` and `log.md`" rule never applies to `.llm/` itself.
* Reserved files (`index.md`, `log.md`) do **not** get provenance sidecars — they are tooling artifacts, not authored concepts.
* Reserved-filename semantics are **suspended inside `.llm/`**: every file there is a `type: llm-log` provenance record, even one named `index-llm.md` or `log-llm.md`. Combined with the mandatory `-llm` suffix, this keeps every provenance file unambiguous.

### `llm-log` file format

A `type: llm-log` file needs only `type` in its frontmatter; `title` and a `timestamp` (last interaction) are useful. It carries **no `authorship`** of its own — an LLM logging itself has no meaningful provenance level. Its body is a reverse-chronological list of interactions, newest first:

```markdown
---
type: llm-log
title: LLM log — spec-okf
timestamp: 2026-07-11T20:30:00Z
---

## 2026-07-11T20:30Z · claude-opus-4-8 · → authorship 2
**Prompt:** <verbatim prompt>
**Change:** one-line summary of what the model did / what changed.

## 2026-07-11T20:00Z · claude-opus-4-8 · → authorship 1
**Prompt:** <verbatim original generating prompt>
**Change:** initial generation.
```

Entry headings use a full ISO 8601 timestamp (not date-grouped like `log.md`) — interaction order *is* the provenance. Record the prompt verbatim and summarize the change. The inline `authorship: 1` block uses this same single-entry format, so `1` → `2` migration is copy-paste.

Only LLM interactions produce entries: human-only edits generate none (they show up in git history and `log.md`). A sidecar persists even if a note is later fully rewritten by a human (`authorship: 5`) — it simply stops gaining entries; provenance history is not deleted.

### `log.md` vs. `.llm/<note>-llm.md`

These never duplicate each other:

* `log.md` — folder-scoped changelog: **what** changed and **when**, human-facing.
* `.llm/<note>-llm.md` — note-scoped provenance: **how** a note's text was produced (prompt, model, resulting authorship), machine-facing.

A `log.md` entry MUST NOT reproduce prompts or model strings; it may note that a concept was LLM-revised and give its new authorship level, and stop there. An `llm-log` entry MUST NOT serve as the folder changelog.

## Conformance

This wiki is OKF v0.1 conformant if:

1. Every concept `.md` file has a parseable YAML frontmatter block.
2. Every frontmatter block has a non-empty `type` field.
3. `index.md` and `log.md`, where present, follow the structure in OKF §6 and §7.
4. *(Wiki extension)* Every folder containing a `type: main` file has both an `index.md` and a `log.md`.

Missing optional fields, unknown `type`/`category` values, out-of-range `authorship` values, missing or partial LLM provenance (inline blocks or `.llm/` sidecars), and broken links are all tolerated — they do not break conformance. LLM provenance is a recommended convention, not a conformance requirement.

## Reference

Full specification: https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md

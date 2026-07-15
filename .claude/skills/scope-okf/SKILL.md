---
name: scope-okf
description: Use when reading, creating, or editing markdown notes in a scope-okf / Open Knowledge Format (OKF) wiki — a git folder of plain markdown files with YAML frontmatter. Covers frontmatter fields (type, category, title, description, tags, resource, id, aliases, see-also, superseded_by, sensitivity, visibility, llm), folder roots (type: main), index.md and log.md, wikilinks and the link resolver, LLM provenance (.llm/ sidecars, authorship levels), and conformance rules. Triggers on: scope-okf, OKF, knowledge wiki, concept document, frontmatter type field, wikilink, llm-log, note provenance.
---

# scope-okf

A **scope-okf wiki** is a directory tree of plain-text markdown files in a git folder — no app, no database. It conforms to the [Open Knowledge Format (OKF) v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md), extended with wiki-specific rules. Every `.md` file (except reserved names) is a **concept document** and MUST begin with YAML frontmatter whose one required field is `type`. A light layer of convention (`type`, optional descriptive fields, reserved filenames `index.md`/`log.md`, the reserved `.llm/` folder, and folder-root notes with `type: main`) makes the collection self-describing for humans and agents alike.

**The full specification is in [reference/scope-okf-spec.md](reference/scope-okf-spec.md).** Read it for the complete field reference (§4), linking/resolver rules (§5), LLM provenance (§8), and conformance (§11). This file is the operating summary; consult the reference whenever a detail is unclear.

## Frontmatter at a glance

```yaml
---
type: <role>                       # REQUIRED — structural role: note | main | llm-log | person | dataset | …
id: <opaque stable slug>           # immutable across renames/moves, e.g. 20260713-9f2a
category: <what kind of thing>     # project | book | topic | article | meta | …
title: <display name>
aliases: [<name>, …]
description: <one-line summary>     # must stand alone; feeds index.md entries
resource: <URI or map of URIs>     # where the real external asset lives
tags: [<tag>, …]                   # lowercase, hyphenated; 2–4 specific ones
timestamp: <ISO 8601 datetime>
llm:
  authorship: <1|2|3|4|5>
  review: <unreviewed|reviewed|verified>
  human-only-lock: <true|false>
sensitivity: <public|internal|confidential|restricted>  # REQUIRED on person / personal-data dataset
visibility: <listed|unlisted|hidden>
superseded_by: <path or id>
see-also: [<path or id>, …]
---
```

Only `type` is required. `type` = structural role; `category` = what kind of thing; `tags` = what it's about.

## Agent operating rules

- **Frontmatter first.** Every note needs a non-empty `type`. Default to `type: note` unless it's a folder root (`main`) or an adopted candidate. Add `title`/`description`/`tags`/`category`/`timestamp` only where genuinely useful.
- **Set `llm.authorship` honestly** about *your own* involvement — don't default to `5`. Most LLM-assisted notes are `2` (human-directed, human never edits prose directly), not `1`. When authorship is `1`–`4`, record provenance: **inline** block at top of body for `1`, a **`.llm/<note>-llm.md` sidecar** (`type: llm-log`) for `2`–`4`.
- **Don't self-certify `review`.** Use `review: unreviewed` unless the human vetted it this session. `reviewed`/`verified` are theirs to assert.
- **Respect `human-only-lock: true`** — propose, wait for explicit go-ahead, then write.
- **Handle `sensitivity` carefully.** Never surface/quote/transmit a `restricted` note; treat `confidential` as need-to-know, unless the human authorized it this session. Set `sensitivity` in the same pass when creating a `type: person` note or a `type: dataset` with personal data (minimum `confidential`) — omitting it breaks conformance.
- **Respect `visibility`.** Don't volunteer `hidden` notes unless named; leave `unlisted` notes out of any index you generate.
- **Folder roots pull in files.** A `type: main` note means the folder MUST also have `index.md` and `log.md` — create or update both.
- **Log your changes.** Add a folder `log.md` entry (date-grouped `YYYY-MM-DD`, newest first) when you add/change notes. Keep prompts/model strings out of `log.md` — those are provenance.
- **Keep ids stable.** Never mint a new `id` for an existing concept on rename/move; record an `alias` so old name-based links keep resolving.
- **Prefer successors.** When a note has `superseded_by`, use the successor and don't edit the superseded note except to fix the pointer.
- **Link portably.** Prefer bundle-relative markdown links (`/path/to/note.md`). Resolver order is `id` → filename basename → `title`/`aliases`; ambiguous/unresolvable references are tolerated, not errors.
- **Don't change the rules unilaterally** and **don't backfill blindly** — don't add required fields/reserved names, or retro-add `llm`/provenance/`sensitivity` to old notes unless you're confident.

## Templates

**Minimal note:**
```markdown
---
type: note
title: My first note
---

Write anything here. Link with [another note](/other-note.md).
```

**Folder root** (`type: main` — folder also needs `index.md` and `log.md`):
```markdown
---
type: main
id: 20260713-abcd
title: Project Name
description: One-line summary of the project.
category: project
tags: [example]
timestamp: 2026-07-13T00:00:00Z
llm:
  authorship: 2
  review: unreviewed
---

Root concept for this folder. See the [index](/project/index.md) and [log](/project/log.md).
```

**Person note** (`sensitivity` required):
```markdown
---
type: person
title: Jane Doe
sensitivity: confidential
resource:
  email: mailto:jane@example.com
  orcid: https://orcid.org/0000-0000-0000-0000
---

Notes about Jane.
```

**`llm-log` sidecar** (`.llm/<note>-llm.md`, newest entry on top):
```markdown
---
type: llm-log
title: LLM log — <note>
timestamp: 2026-07-13T00:00:00Z
---

## 2026-07-13T10:30Z · claude-opus-4-8 · → authorship 2
**Prompt:** <verbatim prompt>
**Change:** <one-line summary>.
```

**`index.md`** (no frontmatter):
```markdown
# Concepts

* [Title](note.md) - short description pulled from the note's description field.
```

**`log.md`** (newest first):
```markdown
# Directory Update Log

## 2026-07-13
* **Creation**: Added [Title](/note.md).
```

## Conformance (the hard rules)

A wiki is OKF v0.1 conformant if: (1) every concept `.md` has parseable YAML frontmatter; (2) every block has a non-empty `type`; (3) `index.md`/`log.md` follow their structures; (4) every folder with a `type: main` file also has `index.md` and `log.md`; (5) every `type: person` note and every personal-data `type: dataset` note has `sensitivity`. Everything else — missing optional fields, unknown vocab values, missing provenance, broken links — is **tolerated**. See §11 in the reference.

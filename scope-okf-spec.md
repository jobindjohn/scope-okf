---
type: note
id: 20260713-spec
category: meta
title: scope-okf — Single-File Specification (LLM edition)
description: Complete, self-contained specification of the scope-okf, for human-agentic knowledge wiki workflows and markdown notes
tags: [okf, meta, spec, llm]
timestamp: 2026-07-13T16:04:46Z
llm:
  authorship: 2
  review: reviewed
  human-only-lock: false
resource: https://github.com/jobindjohn/scope-okf
see-also: [/scope-okf.md, /linking.md, /llm.md]
---

# scope-okf — Single-File Specification (LLM edition)

This is a **self-contained** specification of the scope-okf wiki format. Everything an agent needs to read, create, and edit notes in a scope-okf wiki is in this one file — no other file has to be opened. It consolidates the wiki's source-of-truth spec ([scope-okf.md](/scope-okf.md)) together with the linking rules, the LLM-provenance system, and every per-field guidance doc. Where the canonical multi-file spec and this file ever disagree, [scope-okf.md](/scope-okf.md) wins; this file aims to be a faithful, complete rendering of it.

Requirement language: **MUST**, **MUST NOT**, **SHOULD**, **MAY** carry their usual force. Only the rules in [§11 Conformance](#11-conformance) affect conformance; everything else is a convention that is *tolerated* when unmet (see [§11](#11-conformance) for the exact tolerance list).

---

## 1. The model in one paragraph

A scope-okf wiki is a directory tree of plain-text **markdown files in a git folder** — no app, no database. It conforms to the [Open Knowledge Format (OKF) v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md), extended with a few wiki-specific rules. Every `.md` file (except a couple of reserved filenames) is a **concept document** and MUST begin with a YAML frontmatter block whose one required field is `type`. A light layer of convention — the `type:` field, optional descriptive fields, reserved filenames (`index.md`, `log.md`), a reserved `.llm/` folder, and folder-root notes (`type: main`) — makes the collection self-describing so that both a human and an agent can navigate it and add to it reliably. Optional structure is adopted incrementally; nothing beyond a parseable frontmatter with a non-empty `type` (and one sensitivity rule) is required to stay conformant.

**Adoption stages** (adopt a stage only when it starts paying off; nothing below your current stage is mandatory):

1. **Just notes.** `.md` files with only `type` in frontmatter. This alone is a working wiki.
2. **Describe your notes.** Add `title`, `description`, `tags`, `category` where they earn their place.
3. **Organize into folders with roots.** Give a coherent folder a `type: main` root note (named after the folder) plus `index.md` and `log.md`.
4. **Track history and provenance.** Keep `log.md` current; use the `llm` block and `.llm/` sidecars for provenance.
5. **Full use.** Rich `resource` links, consistent categories, complete logs and provenance, cross-links throughout.

---

## 2. Bundle structure and reserved names

The wiki is a directory tree of markdown files. Any `.md` file is a **concept document**, with exactly two reserved filenames and one reserved folder:

| Name        | Kind    | Purpose                                                                 |
|-------------|---------|-------------------------------------------------------------------------|
| `index.md`  | file    | Optional directory listing. Has **no frontmatter**. See [§9](#9-indexmd). |
| `log.md`    | file    | Optional chronological update log. See [§10](#10-logmd).                 |
| `.llm/`     | folder  | Reserved system folder holding LLM-provenance sidecars (`type: llm-log`) for notes in the same folder. Not part of the browsable concept tree. See [§8](#8-llm-provenance). |

`index.md` and `log.md` are **not** concept documents: they carry no `type` and are excluded from the linking name-index. Everything under `.llm/` is provenance, not concepts.

---

## 3. Concept documents and the frontmatter block

Every concept file MUST begin with a YAML frontmatter block. The full field set, in recommended order:

```yaml
---
type: <Type name>                  # REQUIRED — structural role in the bundle
id: <opaque stable slug>           # Optional, immutable across renames/moves
category: <content classification> # Optional — project | book | topic | article | meta | …
title: <display name>              # Optional
aliases: [<name>, …]               # Optional alternative names for name-based links
description: <one-line summary>     # Optional
resource: <URI, or map of named URIs>  # Optional canonical link(s) to the underlying asset
tags: [<tag>, <tag>, …]            # Optional free-form keywords
timestamp: <ISO 8601 datetime>     # Optional last-modified time
llm:                               # Optional LLM provenance + trust block
  authorship: <1|2|3|4|5>
  review: <unreviewed|reviewed|verified>
  human-only-lock: <true|false>
sensitivity: <public|internal|confidential|restricted>  # required on person / personal-data dataset
visibility: <listed|unlisted|hidden>   # Optional surfacing control
superseded_by: <path or id of the note that replaces this one>  # Optional
see-also: [<path or id>, …]        # Optional machine-readable related-notes relation
---
```

**Required:** `type` only.

**Recommended, in priority order:** `category`, `title`, `description`, `resource`, `tags`, `timestamp`, `llm`, `sensitivity`, `visibility`, `id`, `aliases`, `see-also`, `superseded_by`.

The full field reference is [§4](#4-field-reference). Two overarching distinctions drive most field choices:

- **`type` vs `category` vs `tags`** — `type` (required) is the file's *structural role* in the bundle machinery; `category` (optional, one value) is *what kind of thing* the note documents; `tags` (optional, many) are *what the note is about* for search and cross-cutting association.
- **`sensitivity` vs `visibility`** — `sensitivity` is about the *content* (how private/protected), driving access and sharing; `visibility` is about *exposure* (whether it appears in indexes/search/agent output). Orthogonal; set both when they diverge.

---

## 4. Field reference

Every field except `type` is optional; unknown or out-of-range values are tolerated (see [§11](#11-conformance)). "Open-ended" means the vocabulary is not centrally registered — reuse existing values over inventing near-duplicates.

### 4.1 `type` (required)

The one required field. Marks the file's **structural role** in the bundle, not what it is about. Introduce a new `type` value only if it changes how a note is *handled* (parsed, rendered, or required to carry other files alongside it) — the way `main` does. If a candidate value is just a label for what the note is *about*, it belongs in `category` instead.

- MUST be present and non-empty on every concept file (the only rule whose violation breaks conformance for an ordinary note).
- Open-ended and not centrally registered; unknown values are tolerated.
- `type: main` carries extra weight — see [§7](#7-folder-roots-type-main).

**In use:**

| Type   | Meaning |
|--------|---------|
| `main` | A folder's root/summary concept. Requires `index.md` and `log.md` in the same folder ([§7](#7-folder-roots-type-main)). |
| `note` | An ordinary standing note, not tied to one project. The de facto default. |
| `llm-log` | A provenance sidecar under `.llm/` ([§8](#8-llm-provenance)). Carries no `authorship` of its own. |

**Candidates (brainstormed, adopt by actually using one on a note, then move it to "in use"):**

| Type | Meaning |
|------|---------|
| `person` | A note about an individual. Pairs with the `resource` map form (email, ORCID, …). **Requires** a `sensitivity` field ([§4.11](#411-sensitivity-required-on-person--personal-data-dataset)). |
| `dataset` | A note documenting a dataset. If it holds personal information it **requires** `sensitivity` (minimum `confidential`). |
| `reference` | A note that is mainly a pointer to something external (bookmark, tool) plus light commentary. |
| `draft` / `stub` | An intentionally incomplete note; flag so it can be excluded from search/index. |
| `template` | A reusable skeleton for new notes. Would need its own conformance carve-out (no real content). |
| `redirect` | A thin note whose only job is pointing at another note (renamed/merged concepts), to avoid dead links. Distinct from `superseded_by` — see [§4.13](#413-superseded_by). |

Default new notes to `type: note` unless the note is a folder root (`main`), matches an adopted candidate, or needs a genuinely new structural role.

### 4.2 `category` (optional)

Classifies *what kind of content* a note is — "what is this note, as a thing?" One value, on any concept (not only folder roots). Open-ended; keeping the vocabulary small and reused makes the wiki more navigable.

| Category  | Meaning |
|-----------|---------|
| `project` | A body of work with its own goals and folder (e.g. `scope-okf` itself). |
| `book`    | Notes about a book — summary, highlights, reactions. |
| `topic`   | A standing subject-area note, not tied to one project. |
| `article` | Notes on or reproductions of a single article/paper/post. |
| `meta`    | Notes about the wiki's own conventions. |

Prefer an existing category over inventing one. Don't leave it blank just because a note is small — a one-line `category: article` is more useful long-term than nothing.

### 4.3 `title` (optional)

Human-readable display name — what an index or link text should show instead of the filename. Keep it short (a few words); if it needs a clause, that belongs in `description`. Use normal title casing, not the filename's separators (`title: Authorship`, not `authorship-notes`). For a `type: main` note, `title` conventionally matches the folder name in natural casing. When missing, consumers MAY fall back to a formatted filename — don't rely on this for date/slug/abbreviation filenames.

### 4.4 `description` (optional)

A one-line summary of what the note covers — this is what `index.md` entries are pulled from and what a reader sees to decide whether to open the note. One sentence, plain text (not a link or list), meaningful out of context (avoid "Notes on this topic"). The body usually restates/expands it in the opening line, but `description` MUST stand alone. Keep it factual, not promotional. Update it in the same edit when scope changes — a stale description is worse than a missing one.

### 4.5 `tags` (optional)

A YAML flow list of free-form keywords for search and cross-cutting association — "what is this note *about*?" A note may carry several. Keep them lowercase and hyphenated for multi-word concepts (`knowledge-catalog`, not `Knowledge Catalog`) so they stay greppable. Open-ended; reuse existing tags over near-duplicates (`llm` vs `llms` vs `ai`). Favor 2–4 specific tags over many generic ones. Don't duplicate a word already in `type`/`category`, and don't encode provenance/workflow state in a tag (`llm-generated`) — that's the `llm` block's job.

### 4.6 `resource` (optional)

Records where the real, non-wiki asset the note is *about* or derived from lives — "where does the real thing live?" Points *outside* the wiki (contrast body links and `see-also`, which point *within* it). A note with no external counterpart usually has no `resource`. Two forms:

**Single URI** (the common case):

```yaml
resource: https://example.com/thing
```

**Map of named URIs** (when a concept has more than one canonical link):

```yaml
# a type: person note — each key a contact point or profile
resource:
  email: mailto:jane@example.com
  orcid: https://orcid.org/0000-0000-0000-0000
  linkedin: https://linkedin.com/in/jane
  bluesky: https://bsky.app/profile/jane.bsky.social
  phone: tel:+1-555-0100
```

```yaml
# an article-category note — each key a venue/index the same paper appears under
resource:
  doi: https://doi.org/10.xxxx/yyyy
  pmid: https://pubmed.ncbi.nlm.nih.gov/12345678
  journal: https://journal-site.example.com/article
  proceedings: https://conference.example.com/proceedings/paper
  preprint: https://arxiv.org/abs/xxxx.xxxxx
```

Conventions: write single values as a plain string, not a markdown link. Prefer the most canonical identifier available (DOI > publisher/official URL > archive/mirror). In the map form, use URI schemes where they exist (`mailto:`, `tel:`). Map keys are open-ended but reuse common ones — seen so far: `email`, `phone`, `orcid`, `linkedin`, `bluesky`, `github`, `website`, `mastodon` (people); `doi`, `pmid`, `journal`, `proceedings`, `preprint`, `arxiv` (articles). Reach for the map form once there are several links worth naming individually — not merely because more than one exists. If a link rots, update it in place (and bump `timestamp`).

### 4.7 `id` (optional)

A stable, unique-within-bundle identifier, assigned once at creation and **never changed** — even when the file is renamed or moved. It decouples a note's *identity* from its *path*, so `see-also`/`superseded_by` references and hardened links survive renames.

- Form: a short **opaque** slug — e.g. a date-prefixed base36 string (`20260711-9f2a`) or a ULID. This wiki uses `YYYYMMDD-xxxx`.
- MUST NOT be derived from the title or filename (a rename would invalidate it).
- Write-once and immutable. To fix a typo'd id, treat it like a rename and update every reference. Don't recycle an id from a deleted note.
- Set it on notes you expect to be renamed, moved, merged, or referenced from `see-also`/`superseded_by`. Backfilling onto existing notes is fine but not required.

### 4.8 `aliases` (optional)

A YAML flow list of alternative names for a note, used by name-based links ([§5](#5-linking-and-the-resolver)). Lets a note be reached by an old name, a common abbreviation, or a spelled-out/hyphenated variant without renaming the file.

- Matched at the **lowest** resolver tier, together with `title` and **below** the filename basename — so an alias **never** shadows a real filename.
- As collision-prone as titles; two notes sharing an alias are ambiguous and fall to the tie-break ([§5](#5-linking-and-the-resolver)). Keep aliases distinctive.
- Matching is case-folded with `-`, space, and `_` treated as equivalent (`resource field` = `resource-field` = `resource_field`).
- Prefer a stable `id` over a thicket of aliases when the goal is durable links; aliases are for human-friendly names, not rename-proofing. Record an alias when renaming a note so old name-based links keep resolving until hardened.

### 4.9 `see-also` (optional)

A YAML list of bundle-relative paths (preferred) or `id`s naming genuinely related concepts — a machine-readable relation edge that complements inline body links, letting an agent traverse relationships without parsing prose.

```yaml
see-also: [/frontmatter/id.md, /frontmatter/superseded_by.md]
```

- Points *within* the wiki (contrast `resource`). It is the general, undirected "related to" relation (contrast `superseded_by`, the specific directional "replaced by" one).
- Keep it short — a handful of the most relevant notes, not an exhaustive dump. Conceptually two-way; adding the reciprocal `see-also` makes traversal more reliable, but isn't required.
- Prefer paths for readability; use `id`s where the target is likely to move. Don't duplicate `superseded_by` here — the successor is already reachable through that field.

### 4.10 `superseded_by` (optional)

Marks a note as replaced by a newer one and points to its successor — "is this still current, and if not, what replaces it?" Presence flags the note as **historical**: kept for the record, but no longer the place to look.

```yaml
superseded_by: /frontmatter/llm.md   # or an id: 20260711-ll0m
```

- Points at exactly one successor (bundle-relative path preferred, or an `id`). If a note was split, point at the most central successor and use `see-also` for the rest.
- Distinct from a `type: redirect` note: a superseded note **keeps its own content** (preserved for history, just flagged as overtaken); a redirect has no content of its own and only forwards an old path.
- Set it in the same edit that designates the successor (bump `timestamp`). Consider also lowering the old note's `visibility` to `unlisted`, and record the change in `log.md` as a `**Deprecation**` entry.
- Don't delete a superseded note by default — the pointer plus preserved content beats a gap. Delete only when the old content is actively misleading.

### 4.11 `sensitivity` (required on `person` / personal-data `dataset`)

Classifies how private or protected a note's *content* is, so humans and agents can make sharing and access decisions without reading the whole note. Ordered least → most sensitive; when in doubt between two levels, pick the higher.

| Value | Meaning |
|-------|---------|
| `public` | Nothing sensitive or personal; safe to share freely. |
| `internal` | Not for public distribution, but nothing personally sensitive. |
| `confidential` | Contains personal data (PII) or other sensitive information; limit access and sharing. The **minimum** level for any note holding personal information. |
| `restricted` | Special-category or high-risk data — health, financial, biometric, credentials, government IDs, or otherwise legally protected. Agents MUST NOT surface or transmit these without explicit human authorization. |

**Required** (a conformance rule — [§11](#11-conformance)) on: every `type: person` note (minimum `confidential`), and every `type: dataset` note that contains personal information (minimum `confidential`, or `restricted` if special-category). A `dataset` with no personal data doesn't require it (though `public`/`internal` is still worth setting). Values are a controlled vocabulary — don't invent new ones; if a note doesn't fit, it's almost always `restricted`. Re-evaluate on edit; prefer over-classifying to under-classifying.

### 4.12 `visibility` (optional)

Controls how a note is *surfaced* — whether it shows up in `index.md`, in routine search, and in what an agent volunteers. Absent means `listed`.

| Value | Meaning |
|-------|---------|
| `listed` | Default. Appears in `index.md` and normal search; agents may surface it freely. |
| `unlisted` | Readable if linked, but excluded from index generation and routine search. For stubs, drafts, supporting notes. |
| `hidden` | Agents SHOULD NOT surface or expose it unless the human asks for it by name. For private or set-aside notes. |

`unlisted`/`hidden` notes are still first-class concepts; consumers that build `index.md` skip them. `hidden` is a surfacing hint, not access control — pair it with `sensitivity` when the concern is privacy, not just clutter. Set `visibility` explicitly only when you want non-default behaviour.

### 4.13 `llm` (optional) — provenance and trust block

A map recording an LLM's relationship to a note in one place: how much of the text an LLM produced (`authorship`), whether a human has vetted it (`review`), and whether agents may edit it without sign-off (`human-only-lock`). It replaces the earlier standalone `authorship` scalar (now `llm.authorship`).

```yaml
llm:
  authorship: 2
  review: reviewed
  human-only-lock: false
```

**`authorship` (1–5)** — how the text was produced. A **provenance** record, not a quality signal (a `5` can be wrong; a `1` can be correct — that's why `review` is separate).

| Value | Meaning |
|-------|---------|
| `1` | Fully LLM-generated, autonomous. No human feedback loop, no human edit. |
| `2` | LLM-generated, human-directed. Human gives feedback across one or more rounds; the LLM implements them. Text stays fully LLM-penned — the human never edits the prose directly. |
| `3` | LLM-drafted, human-edited. An LLM produced the text; a human revised it directly. |
| `4` | Human-drafted, LLM-edited. A human produced the text; an LLM revised it. |
| `5` | Fully human-authored. No LLM involvement. |

Higher numbers mean more of the final text is directly attributable to a human hand — but `3` and `4` are not ranked against each other (different workflows, not degrees of trust), and `1` vs `2` both stay fully LLM-penned, differing only in editorial control. The `1` vs `2` line turns on whether there was *any* round of human feedback that led to a revision, not on how the note started: a single up-front instruction with no follow-up ("write a note about X") is still `1`; as soon as a human reacts to a draft and the LLM revises — even just "rename this" — it's `2`. When `authorship` is `1`–`4`, the note also carries detailed provenance (prompt, model): **inline** for `1`, a **`.llm/` sidecar** for `2`–`4` ([§8](#8-llm-provenance)).

**`review` (unreviewed | reviewed | verified)** — the trust signal, deliberately separate from authorship. Absent means `unreviewed`.

| Value | Meaning |
|-------|---------|
| `unreviewed` | No human has vetted the note. Default when absent. |
| `reviewed` | A human has read the note and approved it as a whole. |
| `verified` | A human has checked the note's specific claims/facts for correctness, not just read it. |

`review` can be set even on a fully human note (`authorship: 5`) — a stale hand-written fact is no safer than an agent-written one.

**`human-only-lock` (true | false)** — whether agents may edit the note autonomously. `true` means an agent MUST get explicit human sign-off before changing it; `false`/absent means agents may edit under normal conventions. Use it on notes whose wording is load-bearing (the spec itself, policy notes).

Conventions: every key is optional and tolerated missing/out-of-range. The block reflects the *current* state, not full history — update `authorship` to the level best describing the latest substantive pass, and move `review` back to `unreviewed` if a reviewed note is materially rewritten. Set/update `llm` at the same time you'd bump `timestamp`. Don't let `llm` become a junk drawer — content classification goes in `category`, keywords in `tags`, freshness/sensitivity/superseding in their own fields.

### 4.14 `timestamp` (optional)

Last-modified time as an ISO 8601 datetime (e.g. `2026-07-13T00:00:00Z`). Bump it together with the fields that changed — especially `llm`, `resource`, `superseded_by`.

---

## 5. Linking and the resolver

Notes reference each other in **two spellings resolved by one algorithm**. Both are tolerant wiki extensions over plain markdown; neither is required for conformance.

**Path links (the portable form).** An ordinary markdown link to a bundle-relative path — leading `/` from the wiki root, `.md` included: `[index](/index.md)`, `[Resource](/frontmatter/resource.md)`. Real markdown: renders and degrades gracefully in any tool. The preferred, durable form.

**Wikilinks (the ergonomic form).** A name-based reference in double brackets, no path or extension: `[[resource]]`. An authoring convenience — *not* real markdown; shows up literally as `[[resource]]` in tools that don't understand it. Always reducible to a path link (see [Hardening](#hardening) below).

Both forms, plus the `id`/path references in `see-also` and `superseded_by`, resolve through the **same resolver**.

### The resolver

Any reference resolves to at most one note by trying, in order:

1. **`id`** — exact match against a note's `id`. First, so a hardened reference (`[[<id>]]`) resolves deterministically even if a later file coincidentally shares a name. Since ids are opaque slugs, this branch is effectively machine-facing.
2. **Filename basename** — the filename without `.md` (e.g. `resource` for `frontmatter/resource.md`). The common, human-facing case.
3. **`title`, then `aliases`** — display names. Lowest precedence because optional, mutable, and collision-prone.

**First match wins.** A reference resolving to nothing, or still ambiguous after the tie-break below, is **tolerated** (like any broken link) — but resolution is otherwise **deterministic**, never arbitrary.

**Normalization.** Name matching (basename, `title`, `aliases`) is **case-folded**, and `-`, space, and `_` are equivalent — so `[[see also]]`, `[[See-Also]]`, and `[[see_also]]` all match `see-also.md`. Keeps a bundle behaving the same on case-sensitive and case-insensitive filesystems.

**Excluded from the resolver.** Reserved filenames (`index.md`, `log.md`) and everything under `.llm/` are not concepts and never enter the name index — `[[index]]` has no target. A `type: main` file (named after its folder, e.g. `scope-okf.md`) *is* an ordinary target: `[[scope-okf]]` resolves normally.

### Wikilink syntax

- **Bare name:** `[[resource]]`.
- **Path-qualified name:** `[[frontmatter/resource]]` — **bundle-root-relative, no leading `/`, no `.md`**, used to disambiguate. Never current-file-relative (file-relative resolution would reintroduce the path-dependence `id` exists to remove).
- **Display text:** `[[resource|the resource field]]` — pipe separates target from rendered text.
- **Heading anchor:** `[[resource#Map form]]` — targets a heading by its exact text.
- **Embed:** `![[resource]]` — transcludes the target's body. Optional and the biggest commitment (degrades worst in non-aware tools); treat as advanced.

### Collisions and disambiguation

When a bare name matches more than one note, use **shortest-unique-path disambiguation**: write the bare name when it's unique, add just enough leading path to make it unique when it isn't — `[[resource]]` if there's one, `[[frontmatter/resource]]` if two `resource.md` files exist. Because a plain-file bundle has no interactive UI, adding a note can *retroactively* make an existing bare link ambiguous. Two safeguards:

- **Deterministic tie-break.** When a bare name is still ambiguous, prefer a note in the **same folder** as the linking note, then widen progressively — parent folders and their siblings outward — taking the nearest match.
- **Hardening** removes the ambiguity permanently.

Ambiguous links are tolerated but never nondeterministic.

### Body syntax vs frontmatter syntax

One resolver, two surface syntaxes:

- **Body** uses `[[ ]]` brackets: `See [[resource]].`
- **Frontmatter** (`see-also`, `superseded_by`) keeps writing **bare strings**, not brackets — `[[ ]]` collides with YAML's nested-sequence syntax. The same resolver applies:

  ```yaml
  see-also: [resource, /frontmatter/id.md, 20260711-see2]
  ```

  Each entry is treated as a path if it looks like one, otherwise run through the resolver as a name or `id`.

### Hardening

A wikilink can be *hardened* into portable markdown by resolving it once and recording the result — carrying the resolved `id` so it survives renames, and optionally rewriting into a real path link:

```
[[resource]]   →   [resource](/frontmatter/resource.md)
```

Hardening lets `[[ ]]` be an ergonomic input form without eroding the promise that a bundle's files are correctly linked in any tool. Tooling MAY harden on save; the spec neither requires nor forbids it.

### Gotchas

- **Pipes break tables.** `[[note|display]]` inside a markdown table cell breaks table parsing. Escape the pipe as `\|`, or use a path link inside tables.
- **Non-aware renderers** show `[[ ]]` and `![[ ]]` literally. Prefer path links, or harden, for anything shared outside a wikilink-aware tool.

---

## 6. Concept body

Below the frontmatter, a concept's body is ordinary markdown. Conventions, not requirements:

- The opening line usually restates or expands the `description` so the note stands alone.
- Use path links or wikilinks ([§5](#5-linking-and-the-resolver)) for internal references; `resource` and body links point outside/inside the wiki respectively.
- For a note with `llm.authorship: 1`, an **inline provenance block** sits at the very top of the body, above the content ([§8](#8-llm-provenance)).

---

## 7. Folder roots (`type: main`)

A folder MAY have one concept file whose `type` is `main`, marking it as the root/summary concept for that folder. The file is named **after the folder itself**, not `index.md`:

```
projects/
└── scope-okf/
    ├── scope-okf.md      # type: main — root concept for this folder
    ├── index.md
    ├── log.md
    └── …other concepts
```

**Wiki-extension rule (affects conformance — [§11](#11-conformance)):** any folder containing a `type: main` file MUST also have an `index.md` and a `log.md` in that same folder. (Stricter than base OKF, which treats both as always-optional.)

`main` is **never required per-folder.** Not every folder needs one, and nested subfolders don't inherit a requirement for their own just because a parent or sibling has one. Whether a given folder — or which folder within a related group of subfolders — gets a `main` note is an editorial call by the wiki owner, made when that folder represents a coherent enough unit to deserve a root/summary concept. A cluster of subfolders can share a single ancestor's `main` note as their effective root.

---

## 8. LLM provenance

Notes with LLM involvement in their text (`llm.authorship` `1`–`4`) carry a durable record of *how* that text was produced — the prompt, the model, and the resulting authorship level. This is a **provenance record**, not a quality signal and not a changelog; its job is to let a later reader reconstruct how a note came to say what it says. It is a **recommended convention, not a conformance requirement** — missing or partial provenance is tolerated like any missing optional field.

### Where provenance lives

- **`authorship: 1`** (fully autonomous — a single generating prompt, no human feedback): recorded **inline**, as a single block at the top of the note body (below frontmatter, above content), in the same one-entry format the sidecar uses.
- **`authorship: 2`–`4`** (a human feedback loop exists): recorded in a dedicated sidecar concept with `type: llm-log`, stored in a `.llm/` folder beside the note and named `<note>-llm.md`.

```
projects/scope-okf/
├── scope-okf.md          # the concept — authorship: 2
├── .llm/
│   └── scope-okf-llm.md  # type: llm-log — provenance for scope-okf.md
├── index.md
└── log.md
```

### Migration from `1` to `2`

The first time a note gains a human feedback round, its provenance graduates from inline to a sidecar:

1. Create `.llm/<note>-llm.md`.
2. Move the inline `authorship: 1` block into it as the **oldest** (bottom) entry.
3. Add the new round as the newest entry on top.
4. Remove the inline block from the note body.

Because the inline block already uses the sidecar's single-entry format, the move is copy-paste. Provenance then lives in exactly one place.

### `.llm/` is a reserved system folder

- It is **never** listed in `index.md`.
- The "folder with a `type: main` file MUST also have `index.md` and `log.md`" rule **never applies to `.llm/` itself**.
- Reserved-filename semantics are **suspended inside `.llm/`**: every file there is a `type: llm-log` record, even one named `index-llm.md` or `log-llm.md`. Combined with the mandatory `-llm` suffix, this keeps every provenance file unambiguous.
- Reserved files (`index.md`, `log.md`) get **no** sidecar — they are tooling artifacts, not authored concepts.

### `llm-log` file format

A `type: llm-log` file needs only `type` in frontmatter; `title` and a `timestamp` (the last interaction) are useful. It carries **no `authorship`** of its own. Its body is a reverse-chronological list of interactions, newest first:

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

### What does and doesn't produce an entry

- Only LLM interactions produce entries. Human-only edits generate none (those show up in git history and `log.md`). A sidecar is therefore a partial history by design — a gap doesn't mean missing data.
- A sidecar persists even if a note is later fully rewritten by a human (`authorship: 5`); it simply stops gaining entries. Provenance history is not deleted.
- Don't retroactively backfill provenance you're not sure of. Record what you can attest to and start the trail there.

### Boundary with `log.md`

Provenance and the changelog never duplicate each other:

- `log.md` — folder-scoped changelog: **what** changed and **when**, human-facing.
- `.llm/<note>-llm.md` — note-scoped provenance: **how** a note's text was produced (prompt, model, resulting authorship), machine-facing.

A `log.md` entry MUST NOT reproduce prompts or model strings; it MAY note that a concept was LLM-revised and give its new authorship level, and stop there. An `llm-log` entry MUST NOT serve as the folder changelog.

---

## 9. `index.md`

An `index.md` MAY appear in any directory, including the wiki root, to list that directory's contents. It has **no frontmatter** — just a body grouping links under headings:

```markdown
# Section / Group Heading

* [Title 1](relative-url-1) - short description of item 1
* [Title 2](relative-url-2) - short description of item 2

# Another Section

* [Subdirectory](subdir/) - short description of the subdirectory
```

Descriptions SHOULD be pulled from the linked concept's `description` field when available. `index.md` may be generated by hand or on the fly; consumers may synthesize one if missing. Skip `unlisted` and `hidden` notes ([§4.12](#412-visibility-optional)) and everything under `.llm/` when generating one.

---

## 10. `log.md`

A `log.md` MAY appear at any level to record that scope's update history, as a flat list of date-grouped entries, **newest first**:

```markdown
# Directory Update Log

## 2026-07-11
* **Update**: Added new entry for [Concept Title](/path/to/concept.md).
* **Creation**: Established this directory.

## 2026-07-01
* **Initialization**: Created foundational structure.
```

Date headings MUST use ISO 8601 `YYYY-MM-DD`. The leading bold word (`**Update**`, `**Creation**`, `**Deprecation**`, `**Rename**`, `**Refactor**`, …) is a convention, not a requirement. When you add or change notes in a folder, add an entry to that folder's `log.md`. Do not reproduce prompts or model strings here — that's provenance ([§8](#8-llm-provenance)).

---

## 11. Conformance

A scope-okf wiki is **OKF v0.1 conformant** if:

1. Every concept `.md` file has a parseable YAML frontmatter block.
2. Every frontmatter block has a non-empty `type` field.
3. `index.md` and `log.md`, where present, follow the structure in [§9](#9-indexmd) and [§10](#10-logmd) (OKF §6 and §7).
4. *(Wiki extension)* Every folder containing a `type: main` file has both an `index.md` and a `log.md`.
5. *(Wiki extension)* Every `type: person` note, and every `type: dataset` note that contains personal information, has a `sensitivity` field.

**Tolerated — these do NOT break conformance:** missing optional fields; unknown `type`/`category` values; unknown `sensitivity`/`visibility` values; out-of-range `llm.authorship` values; missing or partial LLM provenance (inline blocks or `.llm/` sidecars); and broken links or `see-also`/`superseded_by`/`id` references. LLM provenance is a recommended convention, not a conformance requirement. Rule 5 (`sensitivity`) is the only one of the wiki-extension fields that affects conformance, and only for `person`/`dataset` notes.

---

## 12. Agent operating rules (for LLMs)

Consolidated do/don't for an agent creating or editing notes in a scope-okf wiki:

- **Frontmatter first.** Every note you create needs YAML frontmatter with a non-empty `type` ([§4.1](#41-type-required)). Default to `type: note` unless it's a folder root (`main`) or matches an adopted candidate. Add `title`, `description`, `tags`, `category`, `timestamp` where genuinely useful — don't pad them.
- **Set `llm.authorship` honestly** ([§4.13](#413-llm-optional--provenance-and-trust-block)). Reflect *your own* involvement — don't default to `5` just because a human asked for the change. Most LLM-assisted notes in a conversational wiki are `2`, not `1`. When `authorship` is `1`–`4`, record provenance: inline for `1`, a `.llm/` sidecar for `2`–`4` ([§8](#8-llm-provenance)).
- **Don't self-certify `review`.** Set `review: unreviewed` on notes you produce unless the human has actually vetted them in the same session. `reviewed`/`verified` are the human's to assert.
- **Respect `human-only-lock: true`** as binding: propose the change, wait for explicit go-ahead, then write.
- **Handle `sensitivity` carefully** ([§4.11](#411-sensitivity-required-on-person--personal-data-dataset)). Never surface, quote, or transmit a `restricted` note, and treat `confidential` as need-to-know, unless the human explicitly authorized it this session. When you create a `type: person` note or a `type: dataset` note with personal data, set `sensitivity` in the same pass — omitting it breaks conformance.
- **Respect `visibility`** ([§4.12](#412-visibility-optional)). Don't volunteer `hidden` notes unless named; leave `unlisted` notes out of any index you generate.
- **Folder roots pull in files.** Creating a `type: main` note means the folder also needs an `index.md` and a `log.md` — create or update both ([§7](#7-folder-roots-type-main)).
- **Log your changes.** When you add or change notes in a folder, add an entry to that folder's `log.md` (date-grouped, newest first, ISO 8601 dates). Keep prompts/model strings out of `log.md` — those belong in provenance.
- **Keep ids stable.** When you move or rename a note that has an `id`, keep the `id` unchanged; never mint a new one for the same concept ([§4.7](#47-id-optional)). Record an `alias` when renaming so old name-based links keep resolving.
- **Prefer successors.** When a note carries `superseded_by`, prefer its successor for answers and don't edit the superseded note's content except to fix the pointer.
- **Link portably.** Use normal markdown links; bundle-relative (`/path/to/note.md`) is preferred over relative paths. Follow the resolver order exactly — `id` → basename → `title`/`aliases` — and treat ambiguous/unresolvable references as tolerated, not errors ([§5](#5-linking-and-the-resolver)).
- **Don't change the rules unilaterally.** Don't add new required fields, reserved filenames, or conformance rules on your own — propose the change, wait for explicit go-ahead, then write.
- **Don't backfill blindly.** Don't retroactively add `llm`, provenance, or `sensitivity` to old notes unless you're confident how they were produced and what they contain.

---

## 13. Quickstart templates

**Minimal conformant note:**

```markdown
---
type: note
title: My first note
---

Write anything here in normal markdown. Link with [another note](/other-note.md).
```

**Folder-root note** (`type: main` — remember the folder also needs `index.md` and `log.md`):

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

**`llm-log` sidecar** (`.llm/<note>-llm.md`, for a note at `authorship` 2–4):

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

---

## Reference

Canonical multi-file source of truth: [scope-okf.md](/scope-okf.md) (with [linking.md](/linking.md), [llm.md](/llm.md), and `frontmatter/*.md`). Base format: Open Knowledge Format v0.1 — https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md

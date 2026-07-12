# spec-okf

## Overview

**spec-okf is a specification for a personal wiki** — a small set of conventions for keeping notes, projects, and reference material as plain markdown files in a git folder, with no app or database required. It is not itself the wiki; it's the rulebook you follow to build and maintain one. Every note ends up a `.md` text file you can read, edit, and search with any tool, and a light layer of convention (a `type:` field at the top of each file, a couple of reserved filenames like `index.md` and `log.md`) makes the collection self-describing, so both you and an AI agent can navigate and add to it reliably.

The conventions build on the [Open Knowledge Format (OKF)](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md), extended with a few wiki-specific rules. The full specification lives in [spec-okf.md](./spec-okf.md), which is the source of truth; this README is just an on-ramp.

**Why markdown and OKF.** Notes, projects, and reference material tend to get scattered across apps that don't talk to each other and don't survive well over time. Markdown in git avoids that: it's readable without tooling, diffable, portable, and works equally well whether a human or an agent is reading or writing it. OKF adds just enough structure to make the content self-describing without locking anything into a specific tool or schema.

If you're new here, you only need to know three things to start: notes are markdown files, each one begins with a small `type:` header, and folders can group them. Everything else is optional and can be adopted as you grow.

## Getting Started

The fastest way to begin — you can add more structure later:

1. **Create a note.** Make a `.md` file anywhere in the folder and give it a frontmatter block with a `type`:

   ```markdown
   ---
   type: note
   title: My first note
   ---

   Write anything here in normal markdown.
   ```

   That's a valid, conformant note. `type` is the only required field.

2. **Link notes together.** Use ordinary markdown links: `[see this](/other-note.md)`. Bundle-relative paths (starting with `/` from the wiki root) are preferred.

3. **Browse.** Open any folder's `index.md` (a plain list of what's inside) to find your way around, or read a folder's `type: main` note if it has one.

That's enough to be useful on day one. When you want the fuller conventions — categories, provenance tracking, folder roots, logs — read [spec-okf.md](./spec-okf.md), which is the source of truth.

## Roadmap: from simple to comprehensive

You don't have to adopt all of OKF at once. A sensible progression:

**Stage 1 — Just notes.** Create `.md` files with only `type` in the frontmatter. Write freely, link with markdown. This alone is a working wiki.

**Stage 2 — Describe your notes.** Start adding the optional fields where they earn their place: `title`, `description`, `tags`, and `category` (e.g. `project`, `book`, `topic`, `article`) so notes become easier to scan and filter.

**Stage 3 — Organize into folders with roots.** When a folder becomes a coherent unit (a project, a book, a subject area), give it a `type: main` root note named after the folder, plus an `index.md` listing its contents and a `log.md` recording changes.

**Stage 4 — Track history and provenance.** Keep `log.md` files up to date (date-grouped, newest first) so you can see what changed and when. Use the `llm` field (`authorship` 1–5, plus `review` and `human-only-lock`) to record whether a note was written by you, an LLM, or somewhere in between, whether a human has vetted it, and whether agents may edit it — with `.llm/` sidecars capturing how AI-written text was produced.

**Stage 5 — Full comprehensive use.** Rich `resource` links (DOIs, ORCIDs, contact points), consistent categories across the whole tree, complete logs and provenance, and cross-links throughout — a fully self-describing knowledge base that a human or an agent can traverse end to end.

Move up a stage only when the extra structure starts paying off. Nothing below your current stage is mandatory to stay conformant.

## Instructions for Humans and LLMs

- Read [spec-okf.md](./spec-okf.md) before creating or editing notes — it's the source of truth, not this README.
- Every note you create needs YAML frontmatter with a non-empty `type`. Add `title`, `description`, `tags`, `timestamp` where they're genuinely useful — don't pad them out.
- Use `category` (project, book, topic, article, …) to classify what kind of content a note is, on any note, not just folder roots.
- Use the `llm` field (`authorship` 1–5, `review`, `human-only-lock`) to record LLM vs. human provenance, whether a human has vetted the note, and whether agents may edit it autonomously — see [frontmatter/llm.md](./frontmatter/llm.md). (For LLMs) Set `authorship` honestly to reflect your own involvement, don't default to fully-human, and don't self-certify `review`.
- Set `sensitivity` on any note holding personal or protected data — it's required on `type: person` and personal-data `type: dataset` notes. Use `visibility` to keep notes out of indexes/search, and `human-only-lock: true` on notes agents shouldn't edit unattended — see [frontmatter/sensitivity.md](./frontmatter/sensitivity.md).
- If a folder represents a coherent unit (project, book, topic…) and needs a root note, name that note after the folder itself and give it `type: main`. Creating a `type: main` note means the folder also needs an `index.md` and a `log.md` — create or update both.
- When you add or change notes in a folder, add an entry to that folder's `log.md` (date-grouped, newest first, ISO 8601 dates).
- Link related notes with normal markdown links; bundle-relative (`/path/to/note.md`) is preferred over relative paths.
- (For LLMs) Don't add new required fields, reserved filenames, or conformance rules to spec-okf.md on your own — propose the change, wait for explicit go-ahead, then write it.

# spec-okf

## What

This is JDJ's personal wiki. It's stored as an [Open Knowledge Format (OKF)](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md) knowledge bundle — a directory tree of plain markdown files with YAML frontmatter, no database or proprietary app required. The exact conventions this wiki follows (required fields, folder-root notes, index/log files) are defined in [SPEC.md](./SPEC.md).

## Why

Notes, projects, and reference material tend to get scattered across apps that don't talk to each other and don't survive well over time. Markdown in git avoids that: it's readable without tooling, diffable, portable, and works equally well whether a human or an agent is reading or writing it. OKF adds just enough structure — a `type` field, some reserved filenames — to make the content self-describing, without locking anything into a specific tool or schema.

## How

Each note is a markdown file with a small YAML frontmatter block (`type` is required; `title`, `description`, `tags`, `timestamp`, `category`, etc. are optional — see SPEC.md for the full list). Folders can represent anything — a project, a book, a topic, an article — and may have a root note (`type: main`, named after the folder) plus an `index.md` listing and a `log.md` history. Notes link to each other with normal markdown links.

To browse: start at a folder's `index.md`, or its `type: main` note if it has one.

## Instructions for Humans and LLMs

- Read [SPEC.md](./SPEC.md) before creating or editing notes — it's the source of truth, not this README.
- Every note you create needs YAML frontmatter with a non-empty `type`. Add `title`, `description`, `tags`, `timestamp` where they're genuinely useful — don't pad them out.
- Use `category` (project, book, topic, article, …) to classify what kind of content a note is, on any note, not just folder roots.
- If a folder represents a coherent unit (project, book, topic…) and needs a root note, name that note after the folder itself and give it `type: main`. Creating a `type: main` note means the folder also needs an `index.md` and a `log.md` — create or update both.
- When you add or change notes in a folder, add an entry to that folder's `log.md` (date-grouped, newest first, ISO 8601 dates).
- Link related notes with normal markdown links; bundle-relative (`/path/to/note.md`) is preferred over relative paths.
- (For LLMs) Don't add new required fields, reserved filenames, or conformance rules to SPEC.md on your own — propose the change, wait for explicit go-ahead, then write it.

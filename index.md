# spec-okf

* [spec-okf](spec-okf.md) - Root concept for this wiki.
* [LLM provenance](llm.md) - How LLM-authored notes record how their text was produced — inline for authorship 1, a type:llm-log sidecar under .llm/ for 2–4.

# frontmatter

* [Type](frontmatter/type.md) - How the type field marks a concept's structural role in the wiki — the one required frontmatter field.
* [Categories](frontmatter/category.md) - How the category field is used to classify concepts in this wiki.
* [LLM](frontmatter/llm.md) - How the llm field records an LLM's relationship to a note — authorship, review/trust status, and whether agents may edit it.
* [Tags](frontmatter/tags.md) - How the tags field is used for free-form keywords and cross-cutting association.
* [Resource](frontmatter/resource.md) - How the resource field records canonical URI(s) for the underlying asset a concept documents.
* [Title](frontmatter/title.md) - How the title field records a concept's display name.
* [Description](frontmatter/description.md) - How the description field records a one-line summary of a concept.
* [Sensitivity](frontmatter/sensitivity.md) - How the sensitivity field classifies how private or protected a note's content is — required on person and personal-data dataset notes.
* [Visibility](frontmatter/visibility.md) - How the visibility field controls whether a note is indexed, searched, and surfaced by agents.
* [Id](frontmatter/id.md) - How the id field gives a note a stable identifier that survives renames and moves.
* [Superseded by](frontmatter/superseded_by.md) - How the superseded_by field marks a note as replaced by a newer one and points to its successor.
* [See also](frontmatter/see-also.md) - How the see-also field records related notes as a machine-readable relation, complementing inline body links.

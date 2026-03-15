# Contributing to villageIdiots Docs

## Structure

This repository follows a **domain-first** organization pattern:

```
section/
├── README.md          ← Index for this section (required)
├── topic.md           ← Topical document
└── subsection/        ← Nested section with its own README.md
```

Every directory must have a `README.md` that:
1. Describes the section in one sentence
2. Contains a table of contents linking to child documents
3. Does not duplicate content that belongs in a child document

## Naming Conventions

| Type | Convention | Example |
|------|-----------|---------|
| Documents | `kebab-case.md` | `signal-research.md` |
| Dated documents | `topic-YYYY-MM-DD.md` | `release-checklist-2026-03-06.md` |
| Directories | `kebab-case/` | `upgrade-plan/` |
| Templates | `topic-template.md` or `topic.md` in `templates/` | `go-no-go.md` |

## Writing Style

- Use present tense for current state, future tense for planned work
- Lead with the key fact or decision, not context
- Use tables for structured comparisons (parameters, feature lists, etc.)
- Use code blocks for all commands, code, and config snippets
- Keep headings to H1 (document title) and H2/H3 (sections/subsections)

## Adding a New Document

1. Choose the correct section (`architecture/`, `setup/`, `development/`, `operations/`, `research/`, `skills/`)
2. Create `section/topic.md`
3. Add a row to the section's `README.md` table of contents
4. If creating a new section, create `section/README.md` and add a row to the root `README.md`

## Updating Existing Documents

- Update the document in place — do not create a new versioned copy (except for dated release artifacts like checklists and signoff packets)
- Update the `Last updated` timestamp at the bottom of the document if present
- Keep tables sorted by relevance (not alphabetically) unless the table is a reference list

## Sections Reference

| Section | What belongs here |
|---------|------------------|
| `architecture/` | System design decisions, data flow, component specs, ADRs |
| `setup/` | How to get things running — install, configure, run |
| `development/` | Roadmap, implementation plans, phase specs |
| `operations/` | How to operate the system — release, monitor, respond |
| `research/` | Empirical findings, analysis results, experiment records |
| `skills/` | Cross-domain competency guides (OpenClaw catalog) |
---
name: project-knowledge-base
description: Maintain a Korean-language, project-specific knowledge base inside the repository at docs/.knowledge. Use when recording project progress, task outcomes, vertical slices, architectural or domain knowledge, decisions, constraints, handoffs, or when a new chat needs to recover the project's current state and context.
---

# Project Knowledge Base

Maintain durable project context in the repository so a user can understand progress and a new Codex chat can resume work without the previous conversation.

## Source of truth

Use `docs/.knowledge/` as the only canonical location. Do not create or update a separate mirror unless the user explicitly requests one. Treat existing user edits as authoritative and preserve unrelated changes.

Expected layout:

```text
docs/.knowledge/
├── index.md
├── status.md
├── vertical-slice-map/
│   └── index.md
├── vertical-slices/
│   └── vs-###-<slug>.md
├── knowledge/
│   ├── index.md
│   └── <topic>.md
└── task-reports/
    └── YYYY-MM-DD-<task>.md
```

Keep `knowledge/` flat. Split a long document into independently useful topic files rather than adding premature subdirectories. Use descriptive prefixes such as `architecture-`, `decision-`, `domain-`, `convention-`, and `constraint-` when they improve discovery.

## Recover context before work

1. Find the repository root and inspect `docs/.knowledge/` explicitly, including hidden paths.
2. Read `index.md` and `status.md`.
3. Read `vertical-slice-map/index.md` and the files for slices related to the requested work.
4. Read the latest relevant task report and linked knowledge documents.
5. If the knowledge base is missing, inspect the repository before creating it; initialize only the files needed for the current task.

Use hidden-aware searches such as:

```bash
rg --hidden --glob '!**/.git/**' '<term>' docs/.knowledge
```

Do not treat an empty or stale document as evidence that work has not happened. Check the code, tests, Git history, and task context before recording a claim.

## Classify information

Choose the smallest durable destination. A single piece of information may update more than one document when it changes both knowledge and current status, but avoid copying paragraphs between files.

### `status.md`

Keep this short and current. Record:

- current project phase and overall state
- completed, active, and blocked work
- important recent changes
- open decisions or risks
- the next concrete tasks

Update it after meaningful work that changes project state. Do not use it as a chronological log.

### `vertical-slice-map/index.md`

Keep the project-level map of slices, their relationships, dependencies, and state. Update it when a slice is created, split, merged, reprioritized, blocked, or completed. Link each entry to its slice document.

### `vertical-slices/`

Use one document per meaningful user-facing or technical vertical slice. Capture its goal, scope, user or system flow, acceptance criteria, implementation boundaries, current state, relevant files, dependencies, and known follow-ups. Update the existing slice document when the work belongs to it; do not create a duplicate slice for an incremental task.

Use filenames such as `vs-001-authentication.md`. Keep the identifier stable after creation.

### `knowledge/`

Record information with value beyond the current task: architecture, domain rules, durable decisions, conventions, constraints, integration behavior, or non-obvious operational knowledge. Prefer one cohesive topic per file. Do not promote every implementation detail or temporary workaround.

Use filenames such as:

```text
architecture-overview.md
decision-auth-session.md
domain-user-lifecycle.md
api-conventions.md
constraint-deployment.md
```

Update an existing knowledge document when the same topic changes. If a document becomes difficult to navigate, split it by cohesive topic and update `knowledge/index.md` and all links.

### `task-reports/`

Create one report for a meaningful completed or paused task. Include:

- objective and scope
- summary of work performed
- files or components changed
- validation performed and results
- decisions and discoveries
- blockers, risks, and follow-up tasks
- links to related slices and knowledge

Use `YYYY-MM-DD-<short-task-name>.md`. Keep reports historical; do not rewrite an old report to represent a later task.

## Record and promote

At task completion or pause:

1. Re-check the diff and validation results.
2. Write or update the relevant task report.
3. Promote only reusable discoveries, decisions, constraints, or conventions into `knowledge/`.
4. Update the affected vertical slice and slice map when scope or state changed.
5. Update `status.md` with the current state and next actions.
6. Update `index.md` or a local index whenever a document is added, renamed, or split.
7. Verify links and report exactly which knowledge files changed.

If the task produced no durable knowledge and no meaningful project-state change, do not create a report merely to create noise. If the user explicitly asks for a record, create the smallest useful report and state that no further promotion was warranted.

## Document conventions

Write all project knowledge base prose in Korean by default, including headings, summaries, status updates, decisions, task reports, and follow-up actions. Preserve code, identifiers, API names, commands, file paths, filenames, and proper nouns in their original form when needed. Follow an explicit user request for another language. Do not broadly translate unrelated historical documents unless requested; write new and materially updated content in Korean.

Use Markdown, lowercase kebab-case filenames, relative links, and concise headings. New documents may use this frontmatter:

```yaml
---
type: knowledge # task-report, vertical-slice, status, or map
status: active # planned, blocked, completed, or superseded
updated: YYYY-MM-DD
related-slices: []
---
```

Use only fields that add value. Never invent dates, completion states, decisions, test results, or links. Mark replaced knowledge as `superseded` and link to its replacement instead of silently deleting historical context.

## Response handoff

After updating the knowledge base, briefly report:

- documents created or updated
- durable knowledge promoted
- current status and next action
- any unresolved blocker or assumption

When starting a new chat, use the same order: `index.md` → `status.md` → slice map → relevant slice → latest task report and knowledge.

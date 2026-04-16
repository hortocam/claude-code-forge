Create an Obsidian-style documentation vault under `docs/` in the current project. The command is idempotent — check for existing files before writing and skip any that already exist.

## Files to Create

Create the following structure. For each file, only create it if it does not already exist.

### `docs/_index.md`

```markdown
---
title: Documentation Vault
type: index
section: root
updated: <today's date in YYYY-MM-DD>
---

This vault contains all project documentation organized by purpose.

## Sections

- [[01-Project/_index|01-Project]] — Workflow rules, conventions, issue tracking, coding standards, git workflow
- [[02-Architecture/_index|02-Architecture]] — System design, agent definitions, component relationships, tooling
- [[03-Decisions/_index|03-Decisions]] — Architecture Decision Records (ADRs)
- [[04-Knowledge/_index|04-Knowledge]] — Reference material, how-tos, external tool docs
- [[05-Journal/_index|05-Journal]] — Orchestrator session notes
- [[06-Archive/_index|06-Archive]] — Retired or superseded documents

## Documents
```

### `docs/01-Project/_index.md`

```markdown
---
title: Project
type: index
section: 01-Project
updated: <today's date in YYYY-MM-DD>
---

This section contains workflow rules, conventions, issue tracking procedures, coding standards, and the git workflow that govern day-to-day development on this project.

## Documents

## Links

- [[_index|Back to Vault Root]]
```

### `docs/02-Architecture/_index.md`

```markdown
---
title: Architecture
type: index
section: 02-Architecture
updated: <today's date in YYYY-MM-DD>
---

This section documents the system design, agent definitions, component relationships, and tooling choices that make up the project's technical architecture.

## Documents

## Links

- [[_index|Back to Vault Root]]
```

### `docs/03-Decisions/_index.md`

```markdown
---
title: Decisions
type: index
section: 03-Decisions
updated: <today's date in YYYY-MM-DD>
---

This section contains Architecture Decision Records (ADRs) — one file per significant decision. Each ADR captures the context, options considered, and rationale for a chosen direction.

## Documents

## Links

- [[_index|Back to Vault Root]]
```

### `docs/04-Knowledge/_index.md`

```markdown
---
title: Knowledge
type: index
section: 04-Knowledge
updated: <today's date in YYYY-MM-DD>
---

This section contains reference material, how-to guides, and documentation for external tools and integrations used in the project.

## Documents

## Links

- [[_index|Back to Vault Root]]
```

### `docs/05-Journal/_index.md`

```markdown
---
title: Journal
type: index
section: 05-Journal
updated: <today's date in YYYY-MM-DD>
---

This section contains orchestrator session notes — one file per session. Use these notes to capture decisions made, work completed, and context for future sessions.

## Documents

## Links

- [[_index|Back to Vault Root]]
```

### `docs/06-Archive/_index.md`

```markdown
---
title: Archive
type: index
section: 06-Archive
updated: <today's date in YYYY-MM-DD>
---

This section holds retired or superseded documents. Files are moved here when they are no longer active but should be preserved for historical reference.

## Documents

## Links

- [[_index|Back to Vault Root]]
```

## Instructions

1. Get today's date in `YYYY-MM-DD` format and substitute it for all `<today's date in YYYY-MM-DD>` placeholders.
2. For each file listed above, check whether the file already exists.
   - If it does **not** exist: create the directory (if needed) and write the file.
   - If it **does** exist: skip it without modifying it.
3. After processing all files, print a summary in this format:

```
scaffold-vault complete:
  created: <list of files created, one per line, or "none">
  skipped: <list of files skipped, one per line, or "none">
```

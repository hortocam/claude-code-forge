# CLAUDE.md — claude-code-forge

> **Purpose:** This repo is the source of truth for a personal Claude AI configuration library — skills, agents, commands, hooks, and other `.claude/` settings. It is also self-hosting: the rules here govern how the repo itself is maintained.

---

## Modes of Operation

This project is worked on in two distinct modes. Both follow the same Git workflow, but the role of the AI agent differs.

### Cowork Mode (planning & bootstrapping)

- The AI acts as the **orchestrator**.
- Always creates a new branch before starting any work.
- Responsible for: clarifying requirements, creating GitHub Issues, drafting plans, and reviewing + merging PRs once approved.
- Does **not** write code directly unless a change is a genuine one-liner that requires no new tests.

### Claude Code Mode (implementation)

- The orchestrator's role is to: document requirements as Issues, organize the backlog, and dispatch work to subagents.
- Subagents do the actual development work, each in their own git worktree on their own branch.
- The orchestrator does **not** do direct development work unless explicitly authorized in advance.

---

## Agent Roles

| Role | Responsibilities |
|---|---|
| **Orchestrator** | Requirements, backlog management, issue creation, subagent dispatch, PR final review & merge, issue close |
| **Subagent** | Implements a single assigned issue; creates branch + worktree, writes code, opens PR |
| **Reviewer** | Automated PR review agent; reviews all PRs, requests changes or approves |

---

## Git Workflow

### Branching

Every unit of work lives on its own branch. Never commit directly to `main`.

**Branch naming convention:**
```
{AGENT-ID}/{ISSUE-ID}-{ISSUE-SLUG}
```

Examples:
- `cowork/12-add-python-skill` — work done in a Cowork session
- `agent-1/34-fix-hook-timeout` — work done by a subagent in Claude Code

The `ISSUE-SLUG` should be a short kebab-case version of the issue title.

### Worktrees

Each subagent operates in its own git worktree (isolated copy of the repo). Worktrees are created at the start of a task and cleaned up once the PR is merged.

### Commits

- Write clear, imperative commit messages (e.g., `Add retry logic to bash hook`).
- Each commit should represent a single logical change.
- Never use `--no-verify` to skip hooks unless explicitly approved by the orchestrator.

### Pull Requests

All changes must be submitted as a Pull Request. The PR flow is:

1. **Subagent** opens PR from their branch → `main`.
2. **Reviewer agent** reviews the PR — approves or requests changes.
3. **Orchestrator** does a final check: confirms the review, verifies all tests pass and coding standards are met.
4. **Orchestrator** merges the PR (merge strategy: TBD — see Open Questions).
5. **Orchestrator** updates and closes the corresponding Issue.

PRs should reference their Issue: include `Closes #<issue-id>` in the PR description.

---

## Issue Tracking

All tasks are tracked as GitHub Issues. This is required for third-party tool integration.

- Every piece of work — features, bugs, chores, docs — gets an Issue before work begins.
- Issues are the unit of work dispatched to subagents.
- The orchestrator creates Issues; subagents do not create new Issues without orchestrator approval (they may flag discovered work in a PR comment).
- Issues are closed by the orchestrator after the corresponding PR is merged.

**Issue fields to populate:**
- Clear title
- Description with acceptance criteria
- Label (e.g., `feature`, `bug`, `chore`, `documentation`)
- Assignee (the subagent or `cowork` for Cowork sessions)

---

## Tooling & Environment

- Secrets are managed via **1Password** (`op run`). See `scripts/cc-startup.sh`.
- The `gh` CLI should be installed and authenticated for issue/PR management from the command line.
- Python dependencies: install with `pip install --break-system-packages`.
- npm globals: install with `npm install -g`.

---

## Coding Standards

> ⚠️ **TBD** — to be defined in a follow-up Issue in the first Claude Code session.

Placeholder items to address:
- Linting / formatting tools and config (per language)
- Test requirements (unit tests required for new logic?)
- File/directory naming conventions within `.claude/`
- Skill and agent file structure standards

---

## Open Questions

These are to be resolved in the first Claude Code planning session and documented as Issues:

1. **Merge strategy** — squash merge, rebase, or standard merge commit?
2. **Reviewer agent** — where is it defined? What model/prompt does it use?
3. **Subagent identity scheme** — how are `AGENT-ID`s assigned and tracked?
4. **Protected branches** — should `main` be protected in GitHub settings?
5. **Hotfix workflow** — expedited path for urgent fixes that bypass normal flow?
6. **Issue templates** — standardized GitHub Issue templates per label type?
7. **CI/CD** — any GitHub Actions for linting, tests, or automated review triggers?
8. **Stale branch/worktree cleanup** — automated or manual?
9. **When subagents discover additional work** — create a draft Issue or comment on the PR?
10. **`.claude/` directory structure** — canonical layout of skills, agents, commands, hooks.

---

## Repository Structure

```
claude-code-forge/
├── CLAUDE.md          # This file — project rules and workflow
├── README.md          # Public-facing project description
├── scripts/           # Helper scripts (startup, utilities)
│   └── cc-startup.sh
└── .claude/           # Claude configuration (skills, agents, commands, hooks)
    ├── skills/        # TBD
    ├── agents/        # TBD
    └── commands/      # TBD
```

---

*This file is intentionally minimal. It captures enough to bootstrap a Claude Code session. The first Claude Code session will plan out the remainder of the project in detail and file Issues for all open questions above.*

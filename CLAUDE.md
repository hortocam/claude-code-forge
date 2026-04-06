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
- The orchestrator does **not** do direct development work unless explicitly authorized in advance by the user.

---

## Agent Roles

Agents are identified by **name**, not a numeric ID.

| Agent Name | Responsibilities |
|---|---|
| `orchestrator` | Requirements, backlog management, issue creation, subagent dispatch, PR final review & merge, issue close |
| `reviewer` | Automated PR review; reviews all PRs, requests changes or approves |
| `skill-developer` | Implements skill-related issues |
| _(others TBD)_ | Additional named agents will be defined as the project grows |

---

## Git Workflow

### Branching

Every unit of work lives on its own branch. Never commit directly to `main`.

**Branch naming convention:**
```
{AGENT-NAME}/{ISSUE-ID}-{ISSUE-SLUG}
```

Examples:
- `orchestrator/12-add-python-skill` — work done by the orchestrator in a Cowork session
- `skill-developer/34-fix-hook-timeout` — work done by the skill-developer subagent

The `ISSUE-SLUG` should be a short kebab-case version of the issue title.

### Worktrees

Each subagent operates in its own git worktree (isolated copy of the repo). Worktrees are created at the start of a task and cleaned up once the PR is merged.

**Stale worktree handling** (see also: Worktree Lifecycle below):
- On session start, the orchestrator checks for abandoned worktrees.
- Before cleaning up a worktree, confirm the associated PR is merged or the issue is closed.
- Interrupted sessions should be resumable — check for an open PR or in-progress branch before discarding work.

### Commits

- Write clear, imperative commit messages (e.g., `Add retry logic to bash hook`).
- Each commit should represent a single logical change.
- Never use `--no-verify` to skip hooks unless explicitly approved by the orchestrator.

### Pull Requests

All changes must be submitted as a Pull Request. The PR flow is:

1. **Subagent** opens PR from their branch → `main`.
2. **Reviewer agent** reviews the PR — approves or requests changes.
3. **Orchestrator** does a final check: confirms the review, verifies all tests pass and coding standards are met.
4. **Orchestrator** performs a **squash merge** into `main`.
5. **Orchestrator** closes the corresponding Issue (GitHub will auto-close if PR includes `Closes #<issue-id>`).

PRs must include `Closes #<issue-id>` in the PR description.

### Branch Protection

`main` is the only long-lived branch. Issue branches are created per-task and deleted after merge. Direct commits to `main` are generally not allowed, with a possible exception for meta-folder updates (e.g., `.claude/` config files) — this will be formalized in a follow-up Issue.

---

## Issue Tracking

All tasks are tracked as GitHub Issues. This is required for third-party tool integration.

- Every piece of work — features, bugs, chores, docs — gets an Issue before work begins.
- Issues are the unit of work dispatched to subagents.
- The orchestrator creates and fully documents Issues; subagents do not create new Issues without orchestrator approval.
- Issues are closed by the orchestrator after the corresponding PR is merged (or auto-closed via `Closes #`).

### Issue Labels

| Label | When to Use | Triggers |
|---|---|---|
| `enhancement` | **Default label** for new features and improvements | Standard implementation workflow |
| `bug` | Something is broken or behaving incorrectly | Bug-specific workflow (TBD) |
| `question` | A decision or clarification is needed | Orchestrator reviews and resolves before assigning |
| `idea` | Early-stage concept not yet ready for implementation | Orchestrator reviews and either converts to `enhancement` or closes |
| `blocked` | Applied when upstream requirements are missing | Orchestrator re-sequences; no subagent work until resolved |
| _(terminal labels TBD)_ | Applied when closing tickets for specific reasons | To be defined in first Claude Code session |

**Required issue fields:**
- Clear title
- Description with acceptance criteria
- Label (default: `enhancement`)
- Assignee (the subagent agent name or `orchestrator` for Cowork sessions)

---

## Handling Discovered Work

When a subagent encounters something unexpected during implementation, there are three cases:

**Case 1 — Tests outside task scope fail:**
The developer documents the failure in the Issue comments (what broke and why), fixes it as part of the same task, and includes all changes in the single PR. No new Issue needed.

**Case 2 — Missed work that doesn't block the current task:**
The developer creates a **draft Issue** (clearly marked as draft/`idea`) with enough context for the orchestrator to evaluate and properly document it. They continue and complete their original task without waiting.

**Case 3 — Upstream requirements missed that block the current task:**
The developer updates the current Issue with their findings and requests that it be set to `blocked`. The orchestrator picks it up, addresses the gaps, re-sequences work as needed, and re-assigns the task once unblocked.

---

## Worktree Lifecycle

1. **Create:** Subagent creates a worktree at task start (branch + worktree together).
2. **Work:** All commits happen inside the worktree.
3. **PR:** Subagent opens PR from inside the worktree.
4. **Merge:** Orchestrator squash-merges the PR.
5. **Cleanup:** Worktree is removed after merge; branch is deleted.

**Session interruption:** If a session is interrupted before the PR is merged, the worktree and branch remain. On next session start, the orchestrator checks for open PRs or in-progress branches and can resume the session rather than starting over. Hooks will be built to assist with this (see Open Questions).

---

## Tooling & Environment

- Secrets are managed via **1Password** (`op run`). See `scripts/cc-startup.sh`.
- The `gh` CLI is installed and authenticated in Claude Code sessions via an injected `GITHUB_TOKEN` environment variable.
- Python dependencies: install with `pip install --break-system-packages`.
- npm globals: install with `npm install -g`.
- The `.env` file is gitignored and must never be committed.

---

## `.claude/` Directory Structure

This repo follows the standard Claude `.claude/` directory structure for defining agents, commands, skills, and hooks. The layout will evolve as each component is built from requirements.

```
.claude/
├── agents/        # Named agent definitions
├── commands/      # Slash commands
├── skills/        # Reusable skill prompts
└── hooks/         # Event hooks (session start, PR open, etc.)
```

---

## Coding Standards

> ⚠️ **TBD** — to be defined as Issues in the first Claude Code session.

Placeholder items to address:
- Linting / formatting tools and config (per language)
- Test requirements (unit tests required for new logic?)
- File/directory naming conventions within `.claude/`
- Skill and agent file structure standards

---

## Open Questions

Remaining items to be resolved in the first Claude Code session and documented as Issues:

1. **Reviewer agent** — where is it defined? What model/prompt does it use? How is it invoked?
2. **Bug workflow** — what is the specific flow for `bug`-labeled Issues vs `enhancement`?
3. **Terminal labels** — what labels are applied at ticket close (e.g., `wont-fix`, `duplicate`, `complete`)?
4. **Branch protection settings** — finalize what (if anything) is exempt from main branch protection.
5. **Session resume hooks** — how does session-start hook detect and surface interrupted worktrees?
6. **Stale worktree cleanup** — what is the precise logic for deciding a worktree is safe to remove?
7. **Orchestrator workflows for `question` / `idea`** — what does the orchestrator actually do when these labels appear?
8. **Draft Issue format** — standard structure for Case 2 discovered-work draft issues.
9. **CI/CD** — deferred; to be revisited once basic workflow is stable.

---

## Repository Structure

```
claude-code-forge/
├── CLAUDE.md          # This file — project rules and workflow
├── README.md          # Public-facing project description
├── scripts/           # Helper scripts (startup, utilities)
│   └── cc-startup.sh
└── .claude/           # Claude configuration (skills, agents, commands, hooks)
    ├── agents/        # TBD
    ├── commands/      # TBD
    ├── skills/        # TBD
    └── hooks/         # TBD
```

---

*This file captures all decisions made during initial bootstrapping. The first Claude Code session will resolve remaining open questions and file Issues for each.*

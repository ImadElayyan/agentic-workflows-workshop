# Exercise 1 — Quick Start with Agentic Workflows

In this exercise you will bootstrap the `gh aw` tooling in your repository and create your first agentic workflow: a **daily digest** that summarises open issues and pull requests.

**Estimated time:** 20 minutes

## Objectives

- Initialise Agentic Workflows in your repository with `gh aw init`
- Understand the files and configuration that are created
- Create and compile a daily digest workflow for issues and pull requests
- Trigger the workflow manually and read the output issue

## How Agentic Workflows Work

Agentic workflows are **natural-language markdown files** (`.github/workflows/<name>.md`) with a small YAML frontmatter block at the top. The frontmatter declares things like the trigger, required tools, and permissions. The markdown body is a plain-English prompt that instructs the AI agent what to do.

Before a workflow can run in GitHub Actions, it must be **compiled** into a lock file (`.github/workflows/<name>.lock.yml`). You commit both the `.md` (human-readable) and the `.lock.yml` (machine-readable) files.

## Part 1 — Initialise Agentic Workflows

From the root of your repository, run:

```bash
gh aw init --engine copilot
```

The command performs non-interactive repository setup for the Copilot engine. In current `gh-aw` releases it creates or updates files including:

- `.gitattributes` — marks compiled lock files as generated
- `.github/skills/agentic-workflows/SKILL.md` — the workflow-authoring dispatcher skill
- `.github/agents/agentic-workflows.md` — the Copilot custom agent
- `.github/mcp.json` — the gh-aw MCP server configuration
- `.github/workflows/copilot-setup-steps.yml` — setup used by the Copilot coding agent
- `.vscode/settings.json` — editor configuration

> [!NOTE]
> `gh aw init` requires write access to the repository. Make sure you are working in your fork.

### Inspect the Generated Files

After `init` completes, explore what was created:

```bash
git status
ls .github/skills/agentic-workflows/
ls .github/agents/
```

> [!TIP]
> Generated files can vary with flags and `gh-aw` versions. Use `gh aw init --help` and `git status` as the source of truth for your installation.

## Part 2 — Create a Daily Digest for Issues and Pull Requests

Create your first workflow using `gh aw new`:

```bash
gh aw new daily-digest
```

In current `gh-aw` releases, a named command creates a heavily commented template rather than opening an AI chat. Replace its frontmatter with this focused configuration:

```yaml
---
name: Daily Digest
on:
  schedule: daily on weekdays
  workflow_dispatch:
permissions:
  contents: read
  issues: read
  pull-requests: read
safe-outputs:
  create-issue:
    max: 1
---
```

Then replace the template body with:

```
Every weekday, create a GitHub issue that summarises all open issues
and pull requests in this repository. Group them by label. Include the
total count, the title, the author, and how long each item has been
open. Title the issue "Daily Digest – <date>".
```

> [!TIP]
> Run `gh aw new --interactive` if you prefer the current CLI wizard. It is a terminal form, not a Copilot Chat session.

### What Gets Created

After creating and editing the workflow, compile it:

```bash
gh aw compile daily-digest
```

You will have:

- `.github/workflows/daily-digest.md` — the human-readable workflow with YAML frontmatter and your prompt
- `.github/workflows/daily-digest.lock.yml` — the compiled machine-readable file for GitHub Actions

Open the markdown file to review your completed workflow:

```bash
cat .github/workflows/daily-digest.md
```

Confirm that it contains the focused frontmatter and plain-English body above, without the unused commented examples from the starter template.

> [!NOTE]
> Keep the agent job read-only. `safe-outputs.create-issue` performs the write in a separately scoped job. Always compile after editing the workflow, including prompt-only edits, and never edit the `.lock.yml` by hand.

## Part 3 — Trigger the Workflow Manually

Commit and push the generated files, then trigger the workflow immediately to test it:

```bash
git add .gitattributes .github/workflows/daily-digest.md .github/workflows/daily-digest.lock.yml
git commit -m "Add daily digest workflow"
git push
```

Once pushed, trigger a manual run:

```bash
gh aw run daily-digest
```

After the run completes (usually under a minute), open GitHub and check the **Issues** tab. You should see a new issue titled **Daily Digest – \<today's date\>**.

> [!NOTE]
> If the repository has no open issues or PRs yet, the digest will say so. That is expected — the workflow is still working correctly.

## Success Criteria

- [ ] `gh aw init --engine copilot` completed and created the skill, agent, MCP, and Copilot setup files
- [ ] `.github/workflows/daily-digest.md` exists in your repository
- [ ] `.github/workflows/daily-digest.lock.yml` exists in your repository
- [ ] The workflow was pushed and triggered without errors
- [ ] A new GitHub issue titled **Daily Digest – \<today's date\>** was created in your repository

---

Once done, proceed to [Exercise 2: Hacker News Daily Digest](./02-second-exercise.md).



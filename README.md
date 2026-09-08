🌐 [Português (Brasil)](README.pt_BR.md) | [Español](README.es.md)

# Agentic Workflows Workshop

This repository contains the hands-on workshop for **Agentic Workflows with GitHub Copilot**. Visit the published site or follow the workshop steps in the `workshop/` directory.

## Start the workshop

**To begin the workshop, start at [workshop/README.md](./workshop/README.md)**

Or visit the [published workshop site](https://copilot-dev-days.github.io/agentic-workflows-workshop).

## Repository Structure

```
├── docs/           # Published HTML site (GitHub Pages)
│   ├── index.html  # Landing page
│   ├── step.html   # Step viewer (renders workshop/ markdown)
│   ├── styles.css
│   ├── light-theme.css
│   └── theme-toggle.js
├── workshop/       # Workshop content (markdown)
│   ├── README.md              # Workshop overview
│   ├── 00-prereqs.md          # Prerequisites & tooling
│   ├── 01-first-exercise.md   # Exercise 1 – Quick Start (gh aw init + daily digest)
│   ├── 02-second-exercise.md  # Exercise 2 – Hacker News Daily Digest
│   ├── 03-chatops-sentiment.md # Exercise 3 – ChatOps Sentiment Analysis
│   ├── 04-review.md           # Review & Next Steps
│   └── images/                # Screenshots and diagrams
├── .github/
│   ├── copilot-instructions.md
│   └── workflows/deploy.yml
├── README.md
└── LICENSE
```

## Publishing

The workshop site deploys automatically to GitHub Pages when you push to `main`. Enable GitHub Pages in your repository settings (Settings → Pages → Source: GitHub Actions).

## Attendee Troubleshooting

`gh-aw` changes frequently. Check your installed version before comparing generated files with workshop screenshots:

```powershell
gh aw version
gh aw --help
```

### `gh aw new` creates a template instead of opening an AI agent

This is expected in current releases. A named command creates a workflow template:

```powershell
gh aw new daily-digest
```

To launch the interactive CLI wizard, run:

```powershell
gh aw new --interactive
```

The wizard is a CLI form, not a GitHub Copilot Chat session. Generated initialization files may also differ between `gh-aw` versions. Use `git status` to see what your installed version created.

### `accepts at most 1 arg(s), received 2`

Retype the option using two ASCII hyphens (`--`). Text copied from formatted documents may contain an en dash (`–`) instead:

```powershell
gh aw new hn-daily-digest --interactive
```

### Compilation says Issues must be enabled

The digest creates a GitHub issue, so Issues must be enabled on each attendee's fork.

In the GitHub portal, open the fork and go to **Settings → General → Features**, then select **Issues**.

Alternatively, use the GitHub CLI:

```powershell
gh repo edit YOUR-USERNAME/agentic-workflows-workshop --enable-issues
gh aw compile daily-digest
```

### The HN digest reports missing data

If the generated issue says requests to the Hacker News API were blocked, allowlist its domain in `.github/workflows/hn-daily-digest.md`:

```yaml
network:
	allowed:
		- defaults
		- hacker-news.firebaseio.com
```

Then compile, commit, and push the updated source and lock file before running it again:

```powershell
gh aw compile hn-daily-digest
git add .github/workflows/hn-daily-digest.md .github/workflows/hn-daily-digest.lock.yml
git commit -m "Allow Hacker News API access"
git push
gh aw run hn-daily-digest
```

### Run Hacker News sentiment analysis from an issue

Post a comment on an issue using a Hacker News story URL:

```text
/hn-sentiment https://news.ycombinator.com/item?id=12345
```

The workflow fetches up to 50 top-level comments from Hacker News and replies on the issue with the positive, negative, and neutral sentiment breakdown.

If the run reports `Permission denied and could not request permission from user` for `curl` or `wget`, keep shell disabled and tell the agent to use the configured `web_fetch` tool explicitly:

```yaml
tools:
  bash: false
  cli-proxy: false
  web-fetch: {}
```

### The Action fails while validating `COPILOT_GITHUB_TOKEN`

The Copilot engine needs authentication. First confirm the missing secret:

```powershell
gh aw secrets bootstrap --engine copilot --non-interactive
```

To configure it in the GitHub portal:

1. Open your GitHub profile **Settings → Developer settings → Personal access tokens → Fine-grained tokens**.
2. Create a fine-grained token with **Account permissions → Copilot Requests: Read**.
3. Open your fork and go to **Settings → Secrets and variables → Actions**.
4. Select **New repository secret**.
5. Name it `COPILOT_GITHUB_TOKEN`, paste the token, and save it.

> [!WARNING]
> Never paste a personal access token into workshop chat, source files, screenshots, or terminal commands that may be recorded.

Trigger a new run after saving the secret:

```powershell
gh aw run daily-digest
```

### The workflow file exists locally but cannot run

Compile, commit, and push both the Markdown source and generated lock file before triggering the remote workflow:

```powershell
gh aw compile daily-digest
git add .github/workflows/daily-digest.md .github/workflows/daily-digest.lock.yml
git commit -m "Add daily digest workflow"
git push
gh aw run daily-digest
```

### Diagnose a failed run

Use the run ID printed by `gh aw run`:

```powershell
gh run view RUN-ID --log-failed
gh aw audit RUN-ID
```

If GitHub says the run is still in progress, wait for it to complete before requesting failed logs.

### Remove the extension

The registered extension name is `aw`:

```powershell
gh extension remove aw
```

If Windows reports that `gh-aw.exe` is in use, close terminals or processes currently running `gh aw`, then retry.

## License

This project is licensed under the terms of the MIT open source license. Please refer to the [LICENSE](./LICENSE) for the full terms.

## Support

This project is provided as-is, and may be updated over time. If you have questions, please open an issue.

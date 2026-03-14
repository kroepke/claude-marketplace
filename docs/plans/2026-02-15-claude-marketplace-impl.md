# Claude Code Marketplace Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Set up kroepke/claude-marketplace as a Claude Code plugin marketplace and convert kroepke/codex-plan-reviewer into a distributable plugin.

**Architecture:** Two repos: the marketplace repo contains `.claude-plugin/marketplace.json` pointing to plugin repos hosted on GitHub. The codex-plan-reviewer repo gets a `.claude-plugin/plugin.json` manifest and its SKILL.md moved into the `skills/` directory convention.

**Tech Stack:** Claude Code plugin system, JSON, Markdown, GitHub CLI (`gh`)

---

### Task 1: Create marketplace.json

**Files:**
- Create: `.claude-plugin/marketplace.json`

**Step 1: Create the `.claude-plugin` directory**

Run: `mkdir -p /home/kroepke/projects/claude-marketplace/.claude-plugin`

**Step 2: Write the marketplace.json file**

Create `.claude-plugin/marketplace.json`:

```json
{
  "name": "kroepke-marketplace",
  "owner": {
    "name": "kroepke"
  },
  "metadata": {
    "description": "Community skills and plugins for Claude Code"
  },
  "plugins": [
    {
      "name": "codex-plan-reviewer",
      "description": "Two-pass adversarial review of design documents and implementation plans using OpenAI Codex CLI",
      "version": "1.0.0",
      "author": {
        "name": "kroepke"
      },
      "repository": "https://github.com/kroepke/codex-plan-reviewer",
      "keywords": ["review", "planning", "codex", "adversarial"],
      "category": "development",
      "source": {
        "source": "github",
        "repo": "kroepke/codex-plan-reviewer",
        "ref": "main",
        "sha": "2891768a25515381edc2e4c0188aa6172b336b1e"
      }
    }
  ]
}
```

**Step 3: Commit**

```bash
git add .claude-plugin/marketplace.json
git commit -m "feat: add marketplace.json with codex-plan-reviewer plugin"
```

---

### Task 2: Create marketplace README.md

**Files:**
- Create: `README.md`

**Step 1: Write the README**

Create `README.md`:

```markdown
# kroepke-marketplace

A Claude Code plugin marketplace for community skills and plugins.

## Installation

Add this marketplace to Claude Code:

```
/plugin marketplace add kroepke/claude-marketplace
```

## Available Plugins

### codex-plan-reviewer

Two-pass adversarial review of design documents and implementation plans using OpenAI Codex CLI. Invokes Codex to review plans section-by-section (pass 1), then holistically (pass 2), feeding critique back for revision.

**Install:**

```
/plugin install codex-plan-reviewer@kroepke-marketplace
```

**Prerequisites:**
- Python 3.10+
- `codex` CLI installed and authenticated (`npm i -g @openai/codex`)

**Source:** [kroepke/codex-plan-reviewer](https://github.com/kroepke/codex-plan-reviewer)
```

**Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add marketplace README with installation instructions"
```

---

### Task 3: Push marketplace repo and set remote

**Step 1: Add the GitHub remote**

Run: `git remote add origin git@github.com:kroepke/claude-marketplace.git`

If the remote already exists, skip this step.

**Step 2: Push to GitHub**

Run: `git push -u origin main`

---

### Task 4: Clone codex-plan-reviewer and create plugin structure

**Step 1: Clone the repo**

Run: `gh repo clone kroepke/codex-plan-reviewer /tmp/codex-plan-reviewer`

**Step 2: Create a feature branch**

Run: `cd /tmp/codex-plan-reviewer && git checkout -b feat/claude-code-plugin`

**Step 3: Create .claude-plugin directory**

Run: `mkdir -p /tmp/codex-plan-reviewer/.claude-plugin`

**Step 4: Write plugin.json**

Create `/tmp/codex-plan-reviewer/.claude-plugin/plugin.json`:

```json
{
  "name": "codex-plan-reviewer",
  "description": "Two-pass adversarial review of design documents and implementation plans using OpenAI Codex CLI",
  "version": "1.0.0",
  "author": {
    "name": "kroepke"
  },
  "repository": "https://github.com/kroepke/codex-plan-reviewer",
  "keywords": ["review", "planning", "codex", "adversarial"]
}
```

**Step 5: Move SKILL.md into skills directory**

```bash
mkdir -p /tmp/codex-plan-reviewer/skills/codex-review
git mv /tmp/codex-plan-reviewer/SKILL.md /tmp/codex-plan-reviewer/skills/codex-review/SKILL.md
```

**Step 6: Commit**

```bash
cd /tmp/codex-plan-reviewer
git add .claude-plugin/plugin.json skills/codex-review/SKILL.md
git commit -m "feat: convert to Claude Code plugin

- Add .claude-plugin/plugin.json manifest
- Move SKILL.md to skills/codex-review/SKILL.md for plugin convention"
```

---

### Task 5: Create PR for codex-plan-reviewer

**Step 1: Push the branch**

Run: `cd /tmp/codex-plan-reviewer && git push -u origin feat/claude-code-plugin`

**Step 2: Create the pull request**

```bash
cd /tmp/codex-plan-reviewer && gh pr create \
  --title "Convert to Claude Code plugin" \
  --body "$(cat <<'EOF'
## Summary
- Add `.claude-plugin/plugin.json` manifest so the repo is recognized as a Claude Code plugin
- Move `SKILL.md` to `skills/codex-review/SKILL.md` following plugin directory conventions
- No changes to scripts or prompts — they remain at the root and are referenced by the skill

## Context
This enables distribution through the [kroepke-marketplace](https://github.com/kroepke/claude-marketplace).

After merging, install via:
```
/plugin marketplace add kroepke/claude-marketplace
/plugin install codex-plan-reviewer@kroepke-marketplace
```
EOF
)"
```

---

### Task 6: Update marketplace SHA after PR merge

> **Note:** This task should only be done after the codex-plan-reviewer PR is merged. It can be skipped for now and done later.

**Step 1: Get the new HEAD SHA**

Run: `gh api repos/kroepke/codex-plan-reviewer/commits/main --jq '.sha'`

**Step 2: Update marketplace.json with new SHA**

Edit `.claude-plugin/marketplace.json` and replace the old SHA with the new one.

**Step 3: Commit and push**

```bash
git add .claude-plugin/marketplace.json
git commit -m "chore: update codex-plan-reviewer SHA to latest main"
git push
```

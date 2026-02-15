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

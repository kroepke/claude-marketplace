# Claude Code Marketplace Design

**Date:** 2026-02-15
**Status:** Approved

## Overview

Set up `kroepke/claude-marketplace` as a Claude Code plugin marketplace, and convert `kroepke/codex-plan-reviewer` into a proper Claude Code plugin so it can be distributed through the marketplace.

## Part 1: Marketplace Repository (`kroepke/claude-marketplace`)

### Structure

```
claude-marketplace/
├── .claude-plugin/
│   └── marketplace.json
└── README.md
```

### `marketplace.json`

Follows the Claude Code marketplace specification. Contains:
- Marketplace name: `kroepke-marketplace`
- Owner: `kroepke`
- One plugin entry: `codex-plan-reviewer`, sourced from GitHub at `kroepke/codex-plan-reviewer`, pinned to commit SHA `2891768a25515381edc2e4c0188aa6172b336b1e`

### User Installation Flow

```
/plugin marketplace add kroepke/claude-marketplace
/plugin install codex-plan-reviewer@kroepke-marketplace
```

## Part 2: Plugin Conversion (`kroepke/codex-plan-reviewer`)

### Current Structure

```
codex-plan-reviewer/
├── .gitattributes
├── SKILL.md              # Skill definition at root (non-standard for plugins)
├── prompts/              # Review prompt templates
│   ├── api.md
│   ├── architecture.md
│   ├── data.md
│   ├── holistic.md
│   └── implementation.md
├── scripts/              # Python review scripts
│   ├── extract_sections.py
│   ├── iterate_section.py
│   ├── review_holistic.py
│   └── review_section.py
```

### Target Structure

```
codex-plan-reviewer/
├── .claude-plugin/
│   └── plugin.json       # NEW: plugin manifest
├── .gitattributes
├── skills/
│   └── codex-review/
│       └── SKILL.md      # MOVED: from root to skills/codex-review/
├── prompts/              # Unchanged
├── scripts/              # Unchanged
```

### `plugin.json`

```json
{
  "name": "codex-plan-reviewer",
  "description": "Two-pass adversarial review of design documents and implementation plans using OpenAI Codex CLI",
  "version": "1.0.0",
  "author": {
    "name": "kroepke"
  },
  "repository": "https://github.com/kroepke/codex-plan-reviewer",
  "license": "MIT",
  "keywords": ["review", "planning", "codex", "adversarial"]
}
```

### SKILL.md Changes

The `SKILL.md` is moved from root to `skills/codex-review/SKILL.md`. No content changes needed — the skill already has correct frontmatter with `name` and `description`.

## Decisions

- **Marketplace name**: `kroepke-marketplace` (simple, identifies the owner)
- **Plugin source pinning**: Pin to specific SHA for reproducibility; update SHA when releasing new versions
- **Skill placement**: `skills/codex-review/` follows the Claude Code convention where the folder name becomes the skill name, namespaced under the plugin name (`codex-plan-reviewer:codex-review`)

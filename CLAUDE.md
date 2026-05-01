# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

Claude Code plugin marketplace. Hosts plugins consumable via `/plugin marketplace add` from this repo. No build, test, or runtime — content is markdown + JSON manifests loaded directly by Claude Code.

## Architecture

Three-level layout:

1. **Marketplace root** — [.claude-plugin/marketplace.json](.claude-plugin/marketplace.json) declares the marketplace name, owner, and the list of plugins. Each entry points to a plugin directory via `source` (relative path).
2. **Plugin directory** (e.g. [csharp-code-guidelines/](csharp-code-guidelines/)) — each contains its own [.claude-plugin/plugin.json](csharp-code-guidelines/.claude-plugin/plugin.json) with `name`, `version`, `description`. The directory name, `marketplace.json` entry name, and `plugin.json` name must match.
3. **Skills** — `<plugin>/skills/<skill-name>/SKILL.md` with YAML frontmatter (`name`, `description`, optional `user-invocable`). The `description` field is the trigger contract — Claude reads it to decide when to auto-invoke the skill.

## Adding a New Plugin

1. Create `<plugin-name>/.claude-plugin/plugin.json` (name matches directory).
2. Create `<plugin-name>/skills/<skill-name>/SKILL.md` with frontmatter.
3. Add an entry to `.claude-plugin/marketplace.json` with matching `name` and `source: "./<plugin-name>"`.
4. Bump the plugin's `version` in `plugin.json` on changes.

## Editing Skills

`SKILL.md` is the entire contract — there is no compile step. Two fields drive behavior:

- `description` — auto-invocation trigger. Phrase it as observable user intent ("when the user asks to..."). Vague descriptions = missed invocations.
- `user-invocable: false` — skill only runs via auto-trigger, not `/skill-name`. Default is invocable.

Existing plugins (`csharp-code-guidelines`, `csharp-write-tests`) encode opinionated C# conventions (functional/data object split, NUnit `Assert.That`, `Create<T>()` helpers over inline mocks). When editing, preserve the prescriptive tone — these override default C# style.

## Validation

No tooling. Manually verify after changes:

- `marketplace.json` and each `plugin.json` parse as valid JSON.
- Plugin `name` fields match across `marketplace.json`, `plugin.json`, and directory name.
- `source` paths in `marketplace.json` resolve to existing directories.

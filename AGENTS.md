# AGENTS.md

This file provides guidance to AI coding agents when working with code in this repository.

## What This Is

A skill plugin containing 7 expert skills for Blender 5.x Python scripting. This is a content-only repo — no build system, no tests, no code to run. All files are Markdown and JSON.

## Architecture

This is a **skill plugin**. The plugin system works as follows:

- `.claude-plugin/plugin.json` — Plugin metadata (name, description, version). This is what the host agent reads to discover the plugin.
- `.claude-plugin/marketplace.json` — Marketplace listing for shared discovery.
- `skills/<skill-name>/SKILL.md` — The main skill file. Must have YAML frontmatter with `name` and `description` fields. The `description` is used to decide when to activate the skill.
- `skills/<skill-name>/references/*.md` — Supporting reference files that SKILL.md points to. These are read on-demand when the skill is active.

## Skill Conventions

Each skill follows the same structure:

1. **SKILL.md** — YAML frontmatter (`name`, `description` with trigger phrases), then:
   - Overview section
   - MCP-First Approach (prefer Blender MCP when available, fall back to Python scripts)
   - Task Decision Tree (maps user intents to sections/references)
   - Recipes/patterns for common tasks
   - Debugging & Optimization section
   - Pointers to reference files

2. **references/python_api.md** — Complete Python code templates with correct `bpy` type strings, ready to copy into scripts

3. **Domain-specific reference** — One of: `node_reference.md`, `constraint_reference.md`, `modifier_reference.md`, `physics_reference.md`, or `data_model_reference.md`

### SKILL.md Frontmatter Format

```yaml
---
name: blender-<domain>
description: This skill should be used when working with Blender <domain>... Triggers on "<keyword1>", "<keyword2>"... If a Blender MCP server is available, prefer using that for direct Blender interaction.
---
```

The `description` field drives skill activation. It must include concrete trigger phrases that match user prompts.

## Editing Guidelines

- All API content targets **Blender 5.x** (Python API via `bpy`). Mark any version-specific features (e.g., "new in 5.0").
- Node/modifier/constraint type strings must be exact (e.g., `GeometryNodeMeshCube`, `CompositorNodeDenoise`, `'INVERSE_KINEMATICS'`). Incorrect type strings break scripts silently.
- Reference tables must include the Python type string column — this is the primary lookup value.
- Code examples should be complete and runnable (imports, object selection, mode switching).
- Keep the MCP-first pattern: every SKILL.md should mention checking for Blender MCP availability.

## Git Conventions

Commits use gitmoji + conventional commits format:

```
🎉 feat: description
📝 docs: description
🐛 fix: description
♻️ refactor: description
```

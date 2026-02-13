# Blender Skills for AI Coding Agents

Expert skills for Blender 5.x Python scripting. Provides complete API references, node/modifier/constraint catalogs, recipes, and debugging guides across 7 domains.

## Skills

| Skill                          | Description                                                                                                  |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------ |
| **blender-geometry-nodes**     | ~373 geometry nodes, procedural modeling patterns, scatter/deformation/simulation zones                      |
| **blender-shader-nodes**       | ~95 shader nodes, PBR/procedural/glass/metal material recipes, Cycles vs EEVEE                               |
| **blender-compositing-nodes**  | ~70-80 compositor nodes, denoise/color grading/keying/glare/DOF recipes, multi-layer EXR output              |
| **blender-python-scripting**   | Operators, panels, add-on structure, handlers, timers, property system, context management, batch processing |
| **blender-animation-rigging**  | Keyframes, FCurves, drivers, ~45 constraints, armatures, IK/FK, shape keys, NLA editor                       |
| **blender-modeling-modifiers** | ~50 modifiers, bmesh API, mesh operators, hard surface/retopology/boolean workflows, sculpt setup            |
| **blender-physics-simulation** | Rigid body, cloth, fluid (Mantaflow smoke/fire/liquid), soft body, particles, force fields, baking           |

## Install

### Claude Code

Add the marketplace and install the plugin:

```bash
claude plugin marketplace add ra100/blender-claude-plugin
claude plugin install blender-skills@blender-claude-marketplace
```

Or from a local checkout:

```bash
claude plugin marketplace add /path/to/blender-claude-plugin
claude plugin install blender-skills@blender-claude-marketplace
```

### Manual

Copy the plugin directory into the agent's plugin search path. The agent needs to read `.claude-plugin/plugin.json` to discover the plugin and `skills/*/SKILL.md` to activate individual skills.

## Usage

The skills activate automatically based on your prompt. Ask Claude to:

- Create a geometry node scatter system
- Write a PBR material with Python
- Set up a compositor denoise + color grading pipeline
- Build a custom Blender operator with undo support
- Rig a character with IK/FK switching
- Add a subdivision + bevel + mirror modifier stack
- Simulate cloth falling onto rigid bodies

Each skill includes a task decision tree, common recipes, debugging guides, and full Python API references with correct type strings.

## Structure

```
skills/
├── blender-geometry-nodes/
│   ├── SKILL.md
│   └── references/
│       ├── node_reference.md
│       └── python_api.md
├── blender-shader-nodes/
│   ├── SKILL.md
│   └── references/
│       ├── node_reference.md
│       └── python_api.md
├── blender-compositing-nodes/
│   ├── SKILL.md
│   └── references/
│       ├── node_reference.md
│       └── python_api.md
├── blender-python-scripting/
│   ├── SKILL.md
│   └── references/
│       ├── python_api.md
│       └── data_model_reference.md
├── blender-animation-rigging/
│   ├── SKILL.md
│   └── references/
│       ├── constraint_reference.md
│       └── python_api.md
├── blender-modeling-modifiers/
│   ├── SKILL.md
│   └── references/
│       ├── modifier_reference.md
│       └── python_api.md
└── blender-physics-simulation/
    ├── SKILL.md
    └── references/
        ├── physics_reference.md
        └── python_api.md
```

## MCP Integration

All skills prefer [Blender MCP](https://github.com/ahujasid/blender-mcp) when available for direct Blender interaction. They fall back to generating Python scripts when MCP is unavailable.

To use with Blender MCP, install and configure the MCP server following the [blender-mcp instructions](https://github.com/ahujasid/blender-mcp). Once connected, the skills will automatically prefer MCP commands over generating standalone scripts.

## License

[MIT](LICENSE)

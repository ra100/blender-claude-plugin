---
name: blender-python-scripting
description: This skill should be used when working with Blender Python scripting (bpy) for custom operators, UI panels, add-on development, context management, handlers, property system, batch processing, or general Blender automation. It applies when creating custom tools, writing add-ons, managing scene data, batch-processing files, registering operators/menus/panels, working with Blender's property system, or scripting any Blender workflow via Python in Blender 5.x. Triggers on "bpy script", "Blender Python", "custom operator", "Blender add-on", "UI panel", "Blender automation", "batch render", "context override", "handler", "property group", or any Blender Python API task. If a Blender MCP server is available, prefer using that for direct Blender interaction.
---

# Blender Python Scripting Expert

## Overview

This skill provides expert guidance for Blender 5.x Python scripting: writing custom operators, building UI panels, developing add-ons, managing context, using handlers and timers, working with the property system, batch processing, and accessing Blender's data model. The reference files contain complete templates and the data model hierarchy.

## MCP-First Approach

When a Blender MCP server is available in the environment, prefer using it for direct interaction with Blender (executing scripts, reading scene data, modifying objects). Fall back to generating Python scripts when MCP is unavailable.

To check for Blender MCP availability, search available tools for "blender" at the start of a session.

## Task Decision Tree

- **"Create a custom operator"** -> Consult `references/python_api.md` for operator templates
- **"Build a UI panel"** -> See Panel templates in `references/python_api.md`
- **"Develop a Blender add-on"** -> Follow the Add-on Structure section below
- **"Batch process files / renders"** -> See Batch Processing patterns
- **"Access scene/object data"** -> Consult `references/data_model_reference.md`
- **"Register a property"** -> See Property System in `references/python_api.md`
- **"Run code on frame change / file load"** -> See Handler patterns in `references/python_api.md`
- **"Context errors / poll failures"** -> See Context Management below

## Add-on Structure

A proper Blender add-on follows this layout:

```
my_addon/
├── __init__.py       # bl_info, register(), unregister()
├── operators.py      # Custom operators
├── panels.py         # UI panels
├── properties.py     # PropertyGroups
├── preferences.py    # AddonPreferences (optional)
└── utils.py          # Shared utilities
```

### Minimal Single-File Add-on

```python
bl_info = {
    "name": "My Add-on",
    "author": "Author",
    "version": (1, 0, 0),
    "blender": (5, 0, 0),
    "location": "View3D > Sidebar > My Tab",
    "description": "Short description",
    "category": "Object",
}

import bpy

class MY_OT_my_operator(bpy.types.Operator):
    bl_idname = "my.my_operator"
    bl_label = "My Operator"
    bl_options = {'REGISTER', 'UNDO'}

    @classmethod
    def poll(cls, context):
        return context.active_object is not None

    def execute(self, context):
        # ... do work ...
        return {'FINISHED'}

class MY_PT_my_panel(bpy.types.Panel):
    bl_label = "My Panel"
    bl_idname = "MY_PT_my_panel"
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = "My Tab"

    def draw(self, context):
        layout = self.layout
        layout.operator("my.my_operator")

classes = (MY_OT_my_operator, MY_PT_my_panel)

def register():
    for cls in classes:
        bpy.utils.register_class(cls)

def unregister():
    for cls in classes:
        bpy.utils.unregister_class(cls)

if __name__ == "__main__":
    register()
```

### Naming Conventions

- Operators: `ADDON_OT_name` → `bl_idname = "addon.name"`
- Panels: `ADDON_PT_name`
- Menus: `ADDON_MT_name`
- UILists: `ADDON_UL_name`
- PropertyGroups: `ADDON_PG_name` (convention, not enforced)
- Headers: `ADDON_HT_name`

## Context Management

### Common Context Issues

Blender operations depend on the current context (active object, mode, area type). Many `bpy.ops.*` calls will fail if context is wrong.

**Rule**: Always check context before calling operators. Use `poll()` methods to guard operators.

### Context Override (Blender 4.x+ / 5.x)

```python
# Modern context override using temp_override (4.0+)
with bpy.context.temp_override(active_object=obj, selected_objects=[obj]):
    bpy.ops.object.delete()

# Override with area/region for UI-dependent ops
area = next(a for a in bpy.context.screen.areas if a.type == 'VIEW_3D')
region = next(r for r in area.regions if r.type == 'WINDOW')
with bpy.context.temp_override(area=area, region=region):
    bpy.ops.view3d.camera_to_view()
```

### Mode Switching

```python
# Safe mode switch
if bpy.context.object and bpy.context.object.mode != 'EDIT':
    bpy.ops.object.mode_set(mode='EDIT')

# Always return to object mode when done
bpy.ops.object.mode_set(mode='OBJECT')
```

### Common Context Gotchas

1. **`bpy.ops` in background mode**: Many viewport operators fail in `--background` mode. Use direct data API (`bpy.data.*`) instead.
2. **Operator context in handlers**: Handlers run outside normal context. Use `bpy.context.temp_override()` or avoid `bpy.ops` in handlers.
3. **Active object is None**: Always check `context.active_object is not None` before operations.
4. **Wrong mode**: Object-mode operators fail in edit mode and vice versa. Check `context.object.mode`.
5. **Depsgraph not updated**: After modifying data, call `bpy.context.view_layer.update()` or `depsgraph.update()` if needed.

## Batch Processing

### Batch Render

```python
import bpy, os

scenes_dir = "/path/to/blend/files"
output_dir = "/path/to/output"

for filename in os.listdir(scenes_dir):
    if filename.endswith(".blend"):
        filepath = os.path.join(scenes_dir, filename)
        bpy.ops.wm.open_mainfile(filepath=filepath)
        bpy.context.scene.render.filepath = os.path.join(
            output_dir, filename.replace(".blend", ".png")
        )
        bpy.ops.render.render(write_still=True)
```

### Batch Object Processing

```python
import bpy

for obj in bpy.data.objects:
    if obj.type == 'MESH':
        # Select only this object
        bpy.ops.object.select_all(action='DESELECT')
        obj.select_set(True)
        bpy.context.view_layer.objects.active = obj
        # Apply all modifiers
        for mod in obj.modifiers:
            bpy.ops.object.modifier_apply(modifier=mod.name)
```

### Command-Line Batch

```bash
blender --background scene.blend --python my_script.py
blender --background --python batch_render.py -- --input /files --output /renders
```

Access custom args after `--`:
```python
import sys
argv = sys.argv[sys.argv.index("--") + 1:]
```

## Debugging Tips

1. **System Console**: Window > Toggle System Console (Windows) or run Blender from terminal
2. **Python Console**: Open the Python Console editor area for interactive testing
3. **Info Editor**: Shows Python equivalents of GUI actions — useful for discovering API calls
4. **`dir()` and `help()`**: Use in Python Console to explore API objects
5. **`bpy.context.copy()`**: Returns a dict of the current context for inspection
6. **Error in modal operator**: Add `print()` statements or use `traceback.print_exc()` in exception handlers

## Property System Quick Reference

| Type | Registration | Values |
| --- | --- | --- |
| `BoolProperty` | `bpy.props.BoolProperty()` | True/False |
| `IntProperty` | `bpy.props.IntProperty()` | min, max, default, step |
| `FloatProperty` | `bpy.props.FloatProperty()` | min, max, default, precision |
| `StringProperty` | `bpy.props.StringProperty()` | default, maxlen, subtype |
| `EnumProperty` | `bpy.props.EnumProperty()` | items=[(id, name, desc)] |
| `FloatVectorProperty` | `bpy.props.FloatVectorProperty()` | size, default, subtype |
| `IntVectorProperty` | `bpy.props.IntVectorProperty()` | size, default |
| `BoolVectorProperty` | `bpy.props.BoolVectorProperty()` | size, default |
| `PointerProperty` | `bpy.props.PointerProperty()` | type=PropertyGroup |
| `CollectionProperty` | `bpy.props.CollectionProperty()` | type=PropertyGroup |

### Property Subtypes

- Float/Int: `'PIXEL'`, `'PERCENTAGE'`, `'FACTOR'`, `'ANGLE'`, `'TIME'`, `'DISTANCE'`
- FloatVector: `'COLOR'`, `'TRANSLATION'`, `'DIRECTION'`, `'VELOCITY'`, `'ACCELERATION'`, `'EULER'`, `'QUATERNION'`, `'XYZ'`
- String: `'FILE_PATH'`, `'DIR_PATH'`, `'FILE_NAME'`, `'BYTE_STRING'`, `'PASSWORD'`

## Python API Reference

For complete templates including operators (basic, modal, file dialog, invoke with props), panels (conditional, sub-panels, collapsible), PropertyGroups, handlers, timers, keymaps, and context overrides, consult `references/python_api.md`.

## Data Model Reference

For the complete Blender data hierarchy (`bpy.data.*`), ID types, object types, mesh/curve/armature data access patterns, and depsgraph usage, consult `references/data_model_reference.md`.

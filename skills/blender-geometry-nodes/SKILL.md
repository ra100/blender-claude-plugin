---
name: blender-geometry-nodes
description: Blender 5.x geometry nodes — procedural modeling, scattering, mesh/curve/volume ops, simulation zones, repeat zones, and scripting node trees via Python (bpy).
---

# Blender Geometry Nodes Expert

## Overview

This skill provides expert guidance for Blender 5.x geometry nodes: designing node setups, writing Python scripts to build node trees programmatically, debugging performance issues, and understanding the complete node catalog. The reference files contain the full node listing and Python API patterns.

## MCP-First Approach

When a Blender MCP server is available in the environment, prefer using it for direct interaction with Blender (creating nodes, modifying node trees, reading scene data). Fall back to generating Python scripts when MCP is unavailable.

To check for Blender MCP availability, search available tools for "blender" at the start of a session.

## Task Decision Tree

- **"Create a geometry node setup for X"** -> Design the node tree, then generate Python code to build it (see Generating Node Trees)
- **"My geometry nodes are slow"** -> Follow the Debugging & Optimization workflow
- **"What node does X?"** -> Consult `references/node_reference.md` for the complete node catalog
- **"How do I connect X to Y?"** -> Check socket compatibility and suggest the correct node chain
- **"Script a geometry node tree"** -> Consult `references/python_api.md` for API patterns

## Generating Node Trees

When creating geometry node setups via Python:

1. Read `references/python_api.md` for the correct API patterns and node type names
2. Structure the script following this pattern:
   - Create the node group
   - Set up the group interface (inputs/outputs)
   - Add all nodes with correct type strings
   - Position nodes in a readable left-to-right layout (x spacing ~200-300, y spacing ~200)
   - Set default values on inputs
   - Create all links between nodes
   - Apply as a modifier to the target object
3. Use Frame nodes to organize complex setups into logical sections
4. Add the Group Input and Group Output nodes explicitly

### Node Type String Conventions

- Most geometry nodes: `GeometryNode<PascalCaseName>` (e.g., `GeometryNodeMeshCube`, `GeometryNodeSetPosition`)
- Math/texture/color nodes shared with shader editor: `ShaderNode<Name>` (e.g., `ShaderNodeMath`, `ShaderNodeTexNoise`, `ShaderNodeValToRGB`)
- Boolean/compare/random: `FunctionNode<Name>` (e.g., `FunctionNodeBooleanMath`, `FunctionNodeCompare`, `FunctionNodeRandomValue`)
- When uncertain about the exact type string, consult `references/python_api.md`

### Socket Types for Group Interface

```
NodeSocketFloat, NodeSocketVector, NodeSocketColor, NodeSocketBool,
NodeSocketInt, NodeSocketString, NodeSocketGeometry, NodeSocketObject,
NodeSocketCollection, NodeSocketMaterial, NodeSocketImage,
NodeSocketRotation, NodeSocketMatrix, NodeSocketMenu
```

## Common Node Patterns

### Scatter System
1. Base mesh (e.g., Grid or any mesh) -> Distribute Points on Faces -> Instance on Points <- Instance geometry
2. Add Random Value for rotation/scale variation on instances
3. Use Realize Instances if further mesh operations are needed on the result

### Procedural Deformation
1. Group Input (Geometry) -> Set Position
2. Feed Position through math operations (Noise Texture -> Vector Math -> Math) to compute offset
3. Add the offset to the original position using Vector Math (Add)

### Conditional Geometry
1. Use Separate Geometry with a selection field to split geometry
2. Process each branch independently
3. Join Geometry to merge results back

### Extrude + Transform Pattern
1. Extrude Mesh (Faces) -> outputs Top selection
2. Use the Top output as selection for Set Position, Scale Elements, or further extrusions

### Curve-Based Generation
1. Curve primitive (Circle, Line, etc.) -> Curve to Mesh with a profile curve
2. Or: Mesh to Curve -> Curve operations -> Curve to Mesh

### Repeat Zone (Iterative Operations)
1. Repeat Input -> processing nodes -> Repeat Output
2. Set iteration count on the Repeat Zone
3. Use the Iteration input to vary behavior per iteration

### Simulation Zone (Physics-Like Behavior)
1. Simulation Input -> processing nodes -> Simulation Output
2. State persists across frames
3. Use Scene Time or frame delta for time-dependent behavior

### Simulation Zone Gotchas

**Critical**: These are hard-won learnings from real projects. Violating them produces subtle bugs.

- **Group Input values don't propagate inside Simulation Zones** — nodes inside the sim zone receive the interface *default* value, not the modifier override. **Workaround**: pass values through as state items on the simulation zone.
- **Object Info works inside Sim Zones** — but only when the object reference is set directly on the node socket (not via Group Input). Set `transform_space = 'ORIGINAL'`.
- **Scene Time works inside Sim Zones** — use for frame-based activation logic (e.g., start effect on frame N).
- **Sim zone geometry freezes after frame 1** — Named Attributes on incoming geometry don't update on subsequent frames. Only state items carry forward.
- **Set Position required after Sim Zone** — position state items track values but don't move vertices. Feed the tracked positions into a Set Position node after the sim zone output.
- **Instance Scale on Collection Info always returns (1,1,1)** — use a separate modifier on the source objects to write scale as a named attribute instead.
- **Empties produce no geometry after Realize Instances** — use single-vertex mesh objects as lightweight proxy objects instead of empties.
- **Capture Attribute anonymous attributes don't survive Realize Instances** — use Store Named Attribute with explicit string names instead.
- **Visibility control** — use Delete Geometry to output empty geometry for hiding. Don't rely on `hide_viewport`/`hide_render` from within geometry nodes.
- **`to_mesh()` cannot realize instances** — Instance on Points output returns 0 verts from `obj.to_mesh()`. Use `evaluated_get(depsgraph)` with `realize_instances=True` on the depsgraph, or add a Realize Instances node in the tree.

### For Each Element Zone (Per-Element Processing)
1. For Each Element Input -> processing -> For Each Element Output
2. Processes each element (point, face, spline, instance) independently
3. New in Blender 5.0

### Bundle System (Blender 5.0)
1. Combine Bundle to group multiple data streams into one connection
2. Separate Bundle or Get Bundle Item to extract data
3. Useful for passing complex data through a single socket

## Debugging & Optimization

### Performance Diagnosis

Common causes of slow geometry node trees, in order of impact:

1. **Realize Instances used too early** - Keep instanced geometry as long as possible. Realizing converts instances to real geometry, multiplying vertex count
2. **Unnecessary attribute computation** - Named attributes and Capture Attribute nodes evaluated on every frame even when static. Use Bake node for static results
3. **Dense point distributions** - Reduce density or use Distribute Points in Grid instead of on Faces for uniform distributions
4. **Subdivision Surface with high levels** - Keep viewport levels low (1-2), use render levels for final output
5. **Boolean operations on complex meshes** - Simplify input meshes or use SDF Grid Boolean for volume-based booleans
6. **Simulation Zone overhead** - Minimize geometry passed through simulation zones; only pass what changes per frame
7. **Nested Repeat Zones** - Complexity compounds. Consider flattening or reducing iteration counts
8. **Large attribute transfers** - Sample Nearest/Sample Index on high-poly meshes. Use spatial indexing (built-in) but reduce source mesh complexity

### Debugging Techniques

- **Viewer node**: Connect intermediate outputs to a Viewer node to inspect geometry at any point in the tree. View in the Spreadsheet editor for attribute data.
- **Mute nodes**: Ctrl+M to mute/unmute nodes to isolate which section causes issues
- **Named attributes in Spreadsheet**: Open the Spreadsheet editor to see all attributes, their domains, and values per element
- **Frame rate overlay**: Enable the FPS overlay (Viewport Overlays) to monitor performance in real-time
- **Node timings**: Enable node timings in the overlay to see which nodes consume the most time
- **Simplify scene**: Use Scene Properties > Simplify to reduce subdivision levels and particle counts during development

### Common Errors and Fixes

- **"Cannot connect" between sockets**: Socket type mismatch. Check `references/node_reference.md` for the correct socket types. Use conversion nodes (e.g., Mesh to Points, Float to Integer)
- **Geometry disappears**: Check the selection input on Set/Delete/Separate nodes. An all-false selection removes everything. Inspect with Viewer node
- **Attributes show as 0/empty**: Domain mismatch. An attribute stored on faces cannot be read on points directly. Use Evaluate on Domain or Capture Attribute to transfer between domains
- **Instances not affected by operations**: Most mesh operations require Realize Instances first. Set Position works on instance origins, not the instanced geometry itself
- **Z-fighting/overlapping faces**: After Extrude Mesh, the original faces remain. Use Delete Geometry with the "Top" selection inverted, or Merge by Distance

## Node Reference

For the complete catalog of all geometry nodes organized by category (Input, Output, Attribute, Curve, Geometry, Mesh, Instances, Point, Volume, Simulation, Color, Texture, Utilities), consult `references/node_reference.md`.

Key categories to search:
- Mesh primitives and operations: grep for "Mesh" in node_reference.md
- Curve primitives and operations: grep for "Curve"
- Point distribution: grep for "Point" or "Distribute"
- Instance management: grep for "Instance"
- Volume/SDF operations: grep for "Volume" or "Grid" or "SDF"
- Utility math: grep for "Math" or "Utilities"

## Python API Reference

For the complete Python API patterns including node type strings, socket types, linking, and modifier application, consult `references/python_api.md`.

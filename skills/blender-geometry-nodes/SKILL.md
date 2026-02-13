---
name: blender-geometry-nodes
description: This skill should be used when working with Blender geometry nodes for procedural modeling, scattering, mesh operations, curve operations, volume manipulation, and node tree debugging/optimization. It applies when creating geometry node setups, scripting node trees via Python (bpy), troubleshooting slow or broken node trees, or designing procedural systems in Blender 5.x. Triggers on "geometry nodes", "procedural modeling in Blender", "scatter system", "node tree", "distribute points", "extrude mesh nodes", or any Blender geometry node workflow. If a Blender MCP server is available, prefer using that for direct Blender interaction.
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
